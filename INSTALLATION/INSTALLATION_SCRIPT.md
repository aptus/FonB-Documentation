Starting from v1.1.0, FonB is now supported by an installation script to automate most of the steps involved in FonB Installation. While the documentation for manual install remains the same, this guide will show how to use the installation script.

The operation of the script is fairly easy, however, please remember that you should have your MySQL credentials with you, as these are the only login name password pairs that you need to provide manually. Rest (other then few prompts) Installation Script handles by its own.

First step is to create a new file using `vim /usr/local/src/fonb-install-script.py`. Once you open this file, paste the following code as it is:

```
#!/usr/bin/env python
import sys, urllib2, tarfile, zipfile, os, getpass, stat, StringIO, platform, shutil, glob, re
from optparse import OptionParser
from distutils import spawn
from ConfigParser import RawConfigParser

Errors = []

class Install(object):
	"""
	Initialize an object of this class to start installation
	"""
	DOWNLOAD_URL = "http://aptus.com/download/fonb/latest/"
	TEMP_PATH = "/tmp/phoneb.tar.gz"
	INSTALL_PATH = "/usr/local/src"
	PORT = "8080"

	def __init__(self):
		global Errors
		log("Downloading FonB")
		download(self.DOWNLOAD_URL, self.TEMP_PATH)
		log("Extracting files")
		"""
		This method MUST run. It modifies self.INSTALL_PATH
		"""
		self.extract()
		log("Creating config files")
		self.PHP_CGI_PATH = spawn.find_executable("php-cgi")
		self.PHP_CGI_PATH = raw_input("php-cgi path[%s]:" % (self.PHP_CGI_PATH)) or self.PHP_CGI_PATH
		while not os.access(self.PHP_CGI_PATH, os.X_OK):
			log("Invalid path. Please provide a valid path.")
			self.PHP_CGI_PATH = raw_input("php-cgi path[%s]:" % (self.PHP_CGI_PATH)) or self.PHP_CGI_PATH
		log("Installing development tools required for compilation.")
		package_check = PackageCheck()
		package_check.base_check()
		log("Verifying php requirements.")
		php_is_correct = php_requirements(self.PHP_CGI_PATH)
		if not php_is_correct:
			log("Attempting to fix php requirements.")
			package_check.php_check()
			php_is_correct = php_requirements(self.PHP_CGI_PATH)
			if not php_is_correct:	
				response = raw_input("Some required php modules are missing. Do you want installations script to try to compile PHP with required modules for you? (It's highly recommended that you continue otherwise FonB might not work properly. It won't affect your existing PHP installation) Y/N:")
				if response[0].lower() == "y":
					compile_path = "/opt/php5.4"
					compile_path = raw_input("Where do you want php to be compiled at?[%s]:" % compile_path) or compile_path
					php_nicely_compiled = compile_php(compile_path)	
					if php_nicely_compiled:
						self.PHP_CGI_PATH = os.path.join(compile_path, "bin", "php-cgi")
						log("Using new php-cgi path: %s" % self.PHP_CGI_PATH)
						Errors.pop()
					else:
						error = "[ ERROR ]: Couldn't compile php properly. Using old php-cgi path. Remember to do it manually later, otherwise PhoneB might not work properly."
						log(error)
						Errors.append(error)
		self.create_config_files()

		active_calls = ActiveCallsSetup()
		log("Modifying %s for setting up Active Calls" % active_calls.config_file)
		if active_calls.setup():
			log("%s successfully modified." % active_calls.config_file)
		else:
			error = "[ WARNING ]: Couldn't modify %s. Active Calls wont work without it. Check %s for details" % (active_calls.config_file, "https://github.com/aptus/FonB-Documentation/blob/master/INSTALLATION/INSTALLATION.md#59-modify-extensions_customconf-to-enable-active-calls")
			Errors.append(error)
			log(error)

		log("Creating init.d script")
		self.create_init_script()
		log("PhoneB installation completed.")
		log("Trying to setup ioncube")
		IoncubeSettings().setup(self.INSTALL_PATH, self.PHP_CGI_PATH)
		log("Checking glibc requirement")
		GlibcCheck(self.INSTALL_PATH).check()
		self.add_mobile_config()
		self.activate()
		log("Trying to start phoneb. By executing /etc/init.d/phoneb start")
		result  = os.system("/etc/init.d/phoneb restart")
		if result == 0:
			freepbx = FreePBX()
			if freepbx.check():
				response = raw_input("Freepbx detected. Do you want script to attempt installation of FonB Freepbx admin module?[y/n]:")[0].lower()
				if response == "y":
					status = freepbx.install()
					if not status:
						log("[ ERROR ]: Couldn't install Freepbx module")
					else:
						log("FreePBX module installed." )
			log("FonB was started. You can access it at http://localhost:%s" % self.PORT)
			log("If for some reason, phoneb doesn't show up on browser, try executing %s manually to debug errors." % os.path.join(self.INSTALL_PATH,"bin", "phoneb"))
			log("You can restart phoneb by running 'service phoneb restart'")
			log("To setup passwords for extensions edit '/etc/phoneb/users.cfg'")
			log("To activate phoneb with licenses use '%s'" % os.path.join(self.INSTALL_PATH, "bin", "./phoneb --activate"))
			log("To report bugs or to find further documentation visit http://aptus.com")
			log("Installation messages have been logged to fonb-setup.log. If problems occured during installation include this file in your bug report.")
		else:
			log("Error occured in starting PhoneB. Try running this script with root privilleges or setup init.d script manually.")

	def activate(self):
		config_parser = FonbConfigParser()
		config_parser.read("/etc/phoneb/phoneb.cfg")
		if not (config_parser.has_section("Demo_License_FonB_V1") or config_parser.has_section("License_FonB_V1") or config_parser.has_section("License_FonB_Mobile_V1") or config_parser.has_section("License_FonB_Highrise_V1") ):
			log("Attempting to activate phoneb")
			os.system(os.path.join(self.INSTALL_PATH, "bin", "./phoneb --activate"))

	def create_init_script(self):
		"""
		"""
		script = init_script(os.path.join(self.INSTALL_PATH, 'bin'))
		if(os.access("/etc/init.d", os.W_OK)):
			file = open("/etc/init.d/phoneb", 'w')
			file.write(script)
			os.chmod("/etc/init.d/phoneb", stat.S_IXUSR)
			file.close()
		else:
			log("write access denied to /etc/init.d. Skipping init.d script creation. You can output init script later by running %s -i %s" % (sys.argv[0], self.PHP_CGI_PATH))

	
	def extract(self):
		"""
		Extracts downloaded file in default or given path
		"""
		new_install_path = ""
		while not os.access(self.INSTALL_PATH, os.W_OK):
				log("Write permissions denied in %s. Run with root privilleges or provide a writeable path." % (self.INSTALL_PATH))
				self.INSTALL_PATH = raw_input("Installation directory[%s]:" % (self.INSTALL_PATH)) or self.INSTALL_PATH
		full_temp_path = self.TEMP_PATH
		tar = tarfile.open(full_temp_path, 'r:gz')
		tarInfo = tar.next()
		tar.extractall(self.INSTALL_PATH)
		extracted_folder = tarInfo.name
		self.INSTALL_PATH = os.path.join(self.INSTALL_PATH, extracted_folder)
		tar.close()
		log("Extracted phoneb in: %s" % (self.INSTALL_PATH) )

	def create_config_files(self):
		config_path = "/etc/phoneb"
		if os.path.exists(config_path):
			if os.access(config_path, os.W_OK):
				log("%s exists and is writeable." % (config_path))
			else:
				log("%s exists but is not writeable." % (config_path)			)
				config_path = os.path.join(self.INSTALL_PATH, "etc")
				log("Write permissions denied for /etc/phoneb. Creating config files in %s." % (config_path))
		elif os.access("/etc", os.W_OK):
			log("Creating phoneb folder in /etc")
			os.makedirs(config_path)
		elif not os.access("/etc", os.W_OK):
			config_path = os.path.join(self.INSTALL_PATH, "etc")
			log("Write permissions denied for /etc/phoneb. Creating config files in %s." % (config_path))
		self.create_phoneb_config_file(config_path)
		self.create_users_config_file(config_path)
	
	def create_phoneb_config_file(self, config_path):
		"""
		Sets up general setup parameters for FonB
		"""
		global Errors
		config = FonbConfigParser()
		log("Creating phoneb.cfg file")
		config.read("/etc/phoneb/phoneb.cfg")
		cdr_setup = CDRSettings()
		data = {
			"PhoneB" : {
				"BaseDir" : self.INSTALL_PATH,
				"UsersConfFile" : os.path.join(config_path, "users.cfg"),
				"ListenPort" : raw_input("FonB Server Port[%s]:" % self.PORT) or self.PORT,
				"LameExec" : LameCheck(self.INSTALL_PATH).get_path(),
				"Debug" : "0",
				"PhpCgiPath" : self.PHP_CGI_PATH,
				"AsteriskMonitorPath" : "/var/spool/asterisk/monitor",
			},
			"WebServer" : {
				"Debug" : "0"
			},
			"WebSocket": {
				"Debug" : "0",
				"EnableAmiUpdates" : "0",
				"EnableSummaryUpdates" : "1",
				"EnableHangupUpdates" : "0",
				"EnableFeedbackUpdates" : "0",
				"EnableErrorUpdates" : "0",
				"EnableClientActionUpdates" : "0"
			},
			"MysqlFonB": MySQLSettings().get(),
			"AMI" : AMISettings().get(),
			"MysqlCdr" : cdr_setup.get(),
		}
		cdr_columns_added = False
		if data["MysqlFonB"]["Username"] == "root":
			cdr_columns_added = cdr_setup.add_highrise_columns(data["MysqlFonB"]["Username"], data["MysqlFonB"]["Password"], data["MysqlCdr"]["Database"])
		if not cdr_columns_added:
			cdr_columns_added = cdr_setup.add_highrise_columns(data["MysqlCdr"]["Username"], data["MysqlCdr"]["Password"], data["MysqlCdr"]["Database"])		
			if not cdr_columns_added:
				error = "[ WARNING ]: Couldn't alter cdr table and add columns for highrise notes. May be columns already exist. If they don't,  check https://github.com/aptus/FonB-Documentation/blob/master/INSTALLATION/INSTALLATION.md#58-modify-asterisk-mysql-cdr-db-for-highrise-entries for how to set them up manually."
				Errors.append(error)
				log(error)
		self.PHP_CGI_PATH = data["PhoneB"]["PhpCgiPath"]
		config.parse_dict_to_config(data)
		file = open(os.path.join(config_path, "phoneb.cfg"), "w")
		config.write(file)
		log("phoneb.cfg created")
		file.close()

	def add_mobile_config(self):
		if os.access("/etc/asterisk/iax_custom.conf", os.W_OK):
			log("Writing fonb mobile config in /etc/asterisk/iax_custom.conf")
			iax_file = open("/etc/asterisk/iax_custom.conf")
			if "#include iax_fonb_mobile.conf" not in iax_file.read():
				iax_file.close()
				iax_file = open("/etc/asterisk/iax_custom.conf", "a")
				iax_file.write("\n#include iax_fonb_mobile.conf")
				iax_file.close()


	def create_users_config_file(self, config_path):
		"""
		Creates users.cfg if it doesn't exist and adds one extension
		"""
		config = FonbConfigParser()
		log("Checking for users.cfg")
		if os.access(os.path.join(config_path, "users.cfg"), os.R_OK):
			log("Users.cfg exists. Skipping.")
		else:
			log("Creating users.cfg file")
			exisiting_extensions_parser = RawConfigParser()
			log("Attempting to import existing extensions.")
			config_files = glob.glob("/etc/asterisk/*.conf")
			for config_file in config_files:
				try:
					exisiting_extensions_parser.read(config_file)
				except:
					pass
			for section in exisiting_extensions_parser.sections():
				if section.isdigit():
					terminal = ""
					context = ""
					try:
						terminal = exisiting_extensions_parser.get(section, "dial")
						context = exisiting_extensions_parser.get(section, "context")
					except:
						pass
					if terminal and context:
						log("Found extension: %s" % section)
						config.add_section(section)
						config.set(section, "Extension", section)
						config.set(section, "Terminal", terminal)
						config.set(section, "Context", context)
						callerid = ""
						try:
							callerid = exisiting_extensions_parser.get(section, "callerid")
						except:
							pass
						if callerid:
							config.set(section, "Name", callerid.split()[:-1][0])
						else:
							config.set(section, "Name", "")
						config.set(section, ";Password", "set the password, only 5 users can have password in a demo license.")
						config.set(section, "Mobile", "")
						config.set(section, "BaseDir", self.INSTALL_PATH)
						config.set(section, "Language", "en")
						config.set(section, "Department", "")
						config.set(section, "Company", "")
						config.set(section, "Spy", "all")
			log("Create a user. (At least one user with valid password needed to login)")
			extension = raw_input("Extension:")
			user = {
				extension : {
					"Extension" : extension,
					"Terminal" : raw_input("Terminal[SIP/%s]:" % (extension)) or ("SIP/%s" % (extension)),
					"Context" : raw_input("Context[from-internal]:") or "from-internal",
					"Name" : raw_input("Name:"),
					"Mobile" : raw_input("Mobile:"),
					"Password" : getpass.getpass("Password:"),
					"BaseDir" : raw_input("BaseDir[%s]:" % (self.INSTALL_PATH)) or self.INSTALL_PATH,
					"Language" : "en",
					"Department" : raw_input("Department[Development]:") or "Development",
					"Spy" : raw_input("Spy[all]:") or "all",
					"Company" : raw_input("Company:"),
				}
			}
			config.parse_dict_to_config(user)
			file = open(os.path.join(config_path, "users.cfg"), "w")
			config.write(file)
			log("users.cfg created")
			file.close()

def version():
	"""
	Called when script is run with -v argument.
	Shows current version of this script
	"""
	log("FonB Installation Script v0.01")
	log("FonB v1.0.5")

class FonbConfigParser(RawConfigParser):
	optionxform = str
	allow_no_value = True
	ordered_sections = dict()
	def parse_dict_to_config(self, data):
		"""
		Parse a dictionary to build config file sections and values
		"""
		for section, options in data.iteritems():
			if section not in self.sections():
				self.add_section(section)
			for key,value in options.iteritems():
				self.set(section, key, value)
	def add_comment(self, section, comment):
		self.set(section, '; %s' % (comment,), None)

	def write(self, fp):
		"""Write an .ini-format representation of the configuration state."""
		if self._defaults:
			fp.write("[%s]\n" % ConfigParser.DEFAULTSECT)
			for (key, value) in self._defaults.items():
				self._write_item(fp, key, value)
			fp.write("\n")
		for section in self._sections:
			fp.write("[%s]\n" % section)
			if section in self.ordered_sections:
				for key in self.ordered_sections[section]:
					self._write_item(fp, key, None)
					self.remove_option(section, key)
			for (key, value) in self._sections[section].items():
				self._write_item(fp, key, value)
			fp.write("\n")

	def set_bulk(self, section, values):
		if isinstance(values, list):
			if not self.has_section(section):
				self.add_section(section)
			self.ordered_sections[section] = values
		elif isinstance(values, dict):
			for key, value in values.iteritems():
				self.set(section, key, value)
			
	def _write_item(self, fp, key, value):
		if value is None:
			fp.write("%s\n" % (key,))
		elif key != "__name__":
			fp.write("%s = %s\n" % (key, str(value).replace('\n', '\n\t')))

	def get(self, section, option):
		val = RawConfigParser.get(self, section, option)
		return val.strip('"').strip("'")

class AMISettings(object):
	"""
	Parses Asterisk manager.conf file and gets AMI credentials
	"""
	def __init__(self, ami_config_path='/etc/asterisk/manager.conf'):
		self.ami_config_path = ami_config_path

	def get(self):
		log("Setting up active calls. If this step is skipped, active calls won't work.")
		ami_data = {
			"ManagerHost" : "localhost",
			"ManagerPort" : "",
			"ManagerUsername" : "",
			"ManagerPassword" : "",
			"Debug" : "1",
		}
		while not os.access(self.ami_config_path, os.R_OK):
			log("AMI file not found or not readable.")
			self.ami_config_path = raw_input("Asterisk manager.conf file[%s](enter 's' to skip):" % self.ami_config_path) or self.ami_config_path
			if self.ami_config_path == 's':
				self.ami_config_path = ''
				break
		if self.ami_config_path:
			log("Reading %s" % self.ami_config_path)
			config = FonbConfigParser()
			file = open(self.ami_config_path)
			config.readfp(file)
			ami_data["ManagerHost"] = "localhost"
			ami_data["ManagerUsername"] = [user for user in config.sections() if user != "general"][0]
			ami_data["ManagerPassword"] = config.get(ami_data["ManagerUsername"], "secret")
			ami_data["ManagerPort"] = config.get("general", "port")
			log("AMI credentials received, as long as they were correct in manager.conf, active calls should work.")
			file.close()
		else:
			log("%s file not found or not readable. Skipping AMI setup. You can set it up later manually." % (self.ami_config_path))
		return ami_data

class CDRSettings(object):
	"""
	Takes care of getting cdr settings and building dictionary
	"""

	def __init__(self, cdr_config_path='/etc/asterisk/cdr_mysql.conf'):
		"""
		Initialize cdr_config_path either as default value or with the value that user supplied
		"""
		self.cdr_config_path = cdr_config_path
		self.config = FonbConfigParser()
	def get(self):
		"""

		"""
		global Errors
		cdr_config_path = self.cdr_config_path
		data = {
			'Username' : '',
			'Password' : '',
			'Database' : '',
			'Hostname' : '',
			'Table' : 'cdr'
		}
		log("Trying to get cdr credentials from %s" % (cdr_config_path))
		while not os.access(cdr_config_path, os.R_OK):
			log("File not found or read permissions denied in %s" % (cdr_config_path))
			cdr_config_path = raw_input("Asterisk cdr_mysql config path[%s](enter 's' to skip):" % (cdr_config_path)) or cdr_config_path
			if cdr_config_path == 's':
				cdr_config_path = ''
				break
		if cdr_config_path:
			file = open(cdr_config_path)
			self.config.readfp(file)
			data['Username'] = self.config.get("global", "user")
			data['Password'] = self.config.get("global", "password")
			data['Hostname'] = self.config.get("global", "hostname")
			data['Database'] = self.config.get("global", "dbname")
		else:
			error = "[ ERROR ]: Couldn't read CDR settings. Call history wont work without it. Set it up manually in /etc/phoneb/phoneb.conf"
			Errors.append(error)
			log(error)
		return data

	def add_highrise_columns(self, username, password, database):
		 db = Mysql(username, password, database)
		 status = db.has_column("cdr", "FonBCallUniqueID") or db.query("ALTER TABLE cdr ADD FonBCallUniqueID VARCHAR(80) NOT NULL;") == 0
		 status = status and (db.has_column("cdr", "FonBCallNotes") or db.query("ALTER TABLE cdr ADD FonBCallNotes VARCHAR(80) NOT NULL;") == 0)
		 status = status and (db.has_column("cdr", "FonBHighriseNoteID") or db.query("ALTER TABLE cdr ADD FonBHighriseNoteID VARCHAR(80) NOT NULL;") == 0)
		 return status


class MySQLSettings(object):
	def __init__(self):
		log("Configuring FonB database")


	def get(self):
		log("FonB needs access to a MySQL database to store data. Please provide MySQL username, password and database name.")
		data = {
			"Username" : raw_input("Mysql username[root]:") or "root",
			"Password" : getpass.getpass("Password:"),
			"Database" : raw_input("Database Name[fonb]:") or "fonb",
			"Hostname" : raw_input("Hostname[localhost]:") or "localhost",
		}
		self.db = Mysql(data["Username"], data["Password"])
		if data['Username'] == 'root' and data['Hostname'] == 'localhost':
			self.create_db(data["Database"])
		elif data['Hostname'] == 'localhost':
			self.check_db(**data)
		else:
			log("Couldn't verify MySQL credentials. We hope they were alright.")
		return data

	def create_db(self, database_name):
		global Errors
		log("Woohoo! got root access to MySQL. Trying to create fonb database.")
		return_code = self.db.query("create database if not exists %s;" % (database_name))
		if return_code != 0:
			error = "[ ERROR ]: Problem in creating database. Check your MySQL credentials."
			Errors.append(error)
			log(error)
		else:
			log("MySQL Database is available.")

	def check_db(self, Username, Password, Database, **kwargs):
		global Errors
		log("Verifying MySQL credentials.")
		if Password:
			response = os.popen("mysql -u %s -p'%s' -e \"SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA WHERE SCHEMA_NAME = '%s';\"" % (Username, Password, Database)).readlines()
		else:
			response = os.popen("mysql -u %s -e \"SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA WHERE SCHEMA_NAME = '%s';\"" % (Username, Database)).readlines()
		if len(response) > 1:
			log("MySQL credentials verified.")
		else:
			error = "[ WARNING ]: There was some problem in verifying MySQL credentials. Make sure they were alright. You can edit them later in /etc/phoneb/phoneb.cfg"
			Errors.append(error)
			log(error)

class GlibcCheck(object):
	def __init__(self, install_path):
		self.install_path = install_path

	def check(self):
		global Errors
		output = os.popen("%s --version 2>&1" % os.path.join(self.install_path, "bin", "phoneb")).readlines()
		if "ld-linux.so" in output[0]:
			error = "[ ERROR ]: Glibc problem detected. Fix it by installing glibc.i686 package."
			log(error)
			Errors.append(error)
			os.system("yum install -y glibc.i686")
			output = os.popen("%s --version 2>&1" % os.path.join(self.install_path, "bin", "phoneb")).readlines()
			if "ld-linux.so" not in output[0]:
				log("Problem Fixed.")
				Errors = Errors[:-1]
		elif "APTUS" not in output[0]:
			error = "[ ERROR ]: Unknown error occured while executing phoneb binary. Data: %s" % output[0]
			log(error)
			Errors.append(error)

class PackageCheck(object):
	"""
	Checks required packages and tries to install them
	"""
	def base_check(self):
		os.system("yum install -y autoconf automake libtool re2c flex bison")
		os.system('yum groupinstall -y "Development Tools"')

	def php_check(self):
		os.system("yum install -y libxml2* openssl* php-xml libcurl libcurl-devel curl*")

class IoncubeSettings(object):
	def __init__(self):
		self.config = FonbConfigParser()
		self.arch = 32 
		if self.is_64bit():
			self.arch = 64

	def setup(self, install_path, php_cgi_path):
		global Errors
		self.php_version = self.get_php_version(php_cgi_path)
		php_path = os.path.join(install_path, "php")
		file = open(os.path.join(php_path, "php.ini"), "r")
		ini_str = '[FonB]\n' + file.read()
		ini_fp = StringIO.StringIO(ini_str)
		self.config.readfp(ini_fp)
		self.config.set("FonB", "zend_extension", "./ioncube%s/ioncube_loader_lin_%s.so" % (self.arch, self.php_version))
		file.close()

		file = open(os.path.join(php_path, "php.ini"), "w")
		self.config.write(file)
		file.close()

		command = "cd %s && %s -q %s" % (php_path, php_cgi_path,"listphonebook.php")
		output = os.popen(command).readlines()[0]
		log(output)
		if output == "FonB Error":
			log("Ioncube succesfully setup.")
		else:
			log("Couldn't setup ioncube loader.")
			Errors.append("[ ERROR ]: Couldn't setup ioncube manually. Please set it up manually in %s." % (os.path.join(php_path, "php.ini")))

	def get_php_version(self, php_cgi_path="php-cgi"):
		php_version = os.popen("echo \"<?php echo phpversion(); ?>\" > /tmp/phpversion && %s -q /tmp/phpversion" % php_cgi_path).readlines()[0]
		return php_version[:3]

	def is_64bit(self):
		return platform.architecture()[0] == "64bit"

class FreePBX(object):
	def __init__(self):
		self.url = "http://github.com/aptus/FonBAdmin-FreePBX-module/archive/master.zip"

	def check(self):
		return os.access("/etc/freepbx.conf", os.R_OK) and os.access("/var/www/html/admin/modules/", os.W_OK)
	def install(self):
		if self.check():
			download(self.url, "/tmp/master.zip")
			archive = zipfile.ZipFile(os.path.join("/tmp", self.url.split("/")[-1]))
			if os.path.exists("/var/www/html/admin/modules/fonbadmin"):
				shutil.rmtree("/var/www/html/admin/modules/fonbadmin")
			archive.extractall("/tmp")
			archive.close()
			shutil.move("/tmp/FonBAdmin-FreePBX-module-master", "/var/www/html/admin/modules/fonbadmin")
			return_code = os.system("amportal a ma install fonbadmin")
			if return_code == 0:
				return_code = os.system("amportal a ma reload")
			return (return_code == 0)
		else:
			return False

class LameCheck(object):

	def __init__(self, install_path):
		self.lame_path = os.path.join(install_path, "bin/lame")

	def get_path(self):
		log("Checking lame installation")
		return_code = os.system("%s --help > /dev/null" % self.lame_path)
		if return_code == 0:
			log("PhoneB lame is working fine. No need to compile.")
			return self.lame_path
		else:
			system_lame_path = spawn.find_executable("lame")
			if system_lame_path and os.system("%s --help > /dev/null" % system_lame_path) == 0:
				log("System already has lame available. Skipping compilation.")
				return system_lame_path
			else:
				log("Lame not working properly. Compiling it from source.")
				self.compile()
				system_lame_path = spawn.find_executable("lame")
				return system_lame_path
	
	def compile(self):
		global Errors
		return_code = os.system("cd /usr/src && wget http://sourceforge.net/projects/lame/files/lame/3.98.4/lame-3.98.4.tar.gz && tar -xf lame-3.98.4.tar.gz && cd lame-3.98.4 && ./configure && make && make install")
		if return_code != 0:
			error = "[ ERROR ]: Couldn't compile lame properly. Call recording wont work without lame."
			log(error)
			Errors.append(error)

class ActiveCallsSetup(object):
	"""
	See https://github.com/aptus/FonB-Documentation/blob/master/INSTALLATION/INSTALLATION.md#59-modify-extensions_customconf-to-enable-active-calls
	
	Reads /etc/asterisk/extensions_custom.conf
	and appends following:
	[OnHold]
	exten => s,1,Answer()
	;exten => s,2,MusicOnHold()
	exten => s,2,Hangup

	exten => hold,1,Answer()
	exten => hold,n,MusicOnHold(,3600)
	exten => hold,n,Hangup

	exten => wait,1,NoCDR()
	;exten => wait,n,StopMixMonitor()
	exten => wait,n,Answer()
	exten => wait,n,UserEvent(FonBCallSwitch,Channel: ${CHANNEL(name)})
	exten => wait,n,Wait(50)
	exten => wait,n,Hangup

	[Conference]
	exten =>  Conference,1,MeetMe(${MEETME_ROOMNUM},dqMx)

	;Conference test extension: admin
	exten =>  admin,1,MeetMe(${MEETME_ROOMNUM},qdMxp)
	exten =>  admin,n,MeetMeAdmin(${MEETME_ROOMNUM},K)

	[Hangup]
	exten => Hangup,1,NoCDR()
	exten => Hangup,n,Hangup()
	"""
	
	def __init__(self, config_file="/etc/asterisk/extensions_custom.conf"):
		self.can_access = False
		self.config_file = config_file
		self.set_config_parser()

	def set_config_parser(self):
		config_parser = FonbConfigParser()
		if os.access(self.config_file, os.W_OK) or (os.access("/etc/asterisk", os.W_OK) and not os.path.exists(self.config_file)):
			self.can_access = True
			try:
				config_parser.OPTCRE = re.compile(
			        r'(?P<option>[^\[]*)'          
			        r'(?P<vi>[^\[]*)'              
			        r'(?P<value>[^\[]*)$'
			        )
				config_parser.read(self.config_file)
				self.config_parser = config_parser
			except:
				self.config_parser = config_parser

	def setup(self):

		if self.can_access:
			file_sections = self.config_parser.sections()
			for section in ["OnHold", "Conference", "Hangup"]:
				if section not in file_sections:
					self.config_parser.add_section(section)
			self.config_parser.set_bulk("OnHold", [
				"exten => s,1,Answer()",
				";exten => s,2,MusicOnHold()",
				"exten => s,2,Hangup",
				"exten => hold,1,Answer()",
				"exten => hold,n,MusicOnHold(,3600)",
				"exten => hold,n,Hangup",
				"exten => wait,1,NoCDR()",
				";exten => wait,n,StopMixMonitor()",
				"exten => wait,n,Answer()",
				"exten => wait,n,UserEvent(FonBCallSwitch,Channel: ${CHANNEL(name)})",
				"exten => wait,n,Wait(50)",
				"exten => wait,n,Hangup",
				])
			self.config_parser.set_bulk("Conference", [
				"exten =>  Conference,1,MeetMe(${MEETME_ROOMNUM},dqMx)",
				";Conference test extension: admin",
				"exten =>  admin,1,MeetMe(${MEETME_ROOMNUM},qdMxp)",
				"exten =>  admin,n,MeetMeAdmin(${MEETME_ROOMNUM},K)",
				])
			self.config_parser.set_bulk("Hangup", [
				"exten => Hangup,1,NoCDR()",
				"exten => Hangup,n,Hangup()",
				])
			fp = open(self.config_file, "w")
			self.config_parser.write(fp)
			fp.close()
			log("Restarting asterisk")
			os.system("service asterisk restart")
			return True
		else:
			return False


def init_script(demon_path):
	"""
	Outputs init script
	"""
	demon_path = demon_path.rstrip('/')
	return """
#!/bin/bash
# phoneb daemon
# chkconfig: 345 20 80
# description: phoneb daemon
# processname: phoneb

DAEMON_PATH="%s"                     #Path Location of phoneb executable

DAEMON="phoneb"

NAME="phoneb"
DESC="This Demon controls FonB"
PIDFILE="/var/run/$NAME.pid"
SCRIPTNAME="/etc/init.d/$NAME"

case "$1" in
start)
		printf "%%-50s" "Starting $NAME..."
		if [ -f $PIDFILE ]; then
			PID=`cat $PIDFILE`
			if [ -z "`ps axf | grep ${PID} | grep -v grep`" ]; then
				printf "%%s\n" "Process dead but pidfile exists. Use /etc/init.d/phoneb restart"
			else
				echo "Process already running pid: $PID"
			fi
		else
				PID=`$DAEMON_PATH/$DAEMON  >> /var/log/messages 2>&1 & echo $!`     #for Ubuntu 11.04 or above, use
																			#/var/log/syslog instead
				#echo "$DAEMON > /dev/null 2>&1 & echo $!"
				if [ -z $PID ]; then
						printf "%%s\n" "Fail"
				else
						echo $PID > $PIDFILE
						printf "%%s\n" "Ok"
						#echo "Saving PID" $PID " to " $PIDFILE
				fi
		fi
;;
status)
		printf "%%-50s" "Checking $NAME..."
		if [ -f $PIDFILE ]; then
			PID=`cat $PIDFILE`
			if [ -z "`ps axf | grep ${PID} | grep -v grep`" ]; then
				printf "%%s\n" "Process dead but pidfile exists"
			else
				echo "Running"
			fi
		else
			printf "%%s\n" "Service not running"
		fi
;;
stop)
		printf "%%-50s" "Stopping $NAME"
			PID=`cat $PIDFILE`
			cd $DAEMON_PATH
		if [ -f $PIDFILE ]; then
			kill -HUP $PID
			printf "%%s\n" "Ok"
			rm -f $PIDFILE
		else
			printf "%%s\n" "pidfile not found"
		fi
;;

restart)
		$0 stop
		$0 start
;;

*)
		echo "Usage: $0 {status|start|stop|restart}"
		exit 1
esac
"""	% demon_path
#####################end of init_script############

def php_requirements(php_cgi_path):
	php_modules = os.popen("%s -m" % php_cgi_path).readlines()
	php_modules = [module.strip() for module in php_modules]
	required_modules = [
		"curl",
		"date",
		"dom",
		"json",
		"libxml",
		"mysql",
		"openssl",
		"SimpleXML",
		"xml",
		"xmlreader",
		"xmlwriter"
	]
	missing_modules = [module for module in required_modules if module not in php_modules]
	if len(missing_modules) > 0:
		Errors.append("[ WARNING ]: These required modules are missing from your php installation: %s" % ', '.join(missing_modules))
		log(Errors[-1:][0])
		return False
	else:
		return True

def download(url, temp_path = '/tmp/fonb'):
		"""
		Downloads FonB and shows progress bar
		"""
		file_name = temp_path

		u = urllib2.urlopen(url)
		f = open(file_name, 'wb')
		meta = u.info()
		if meta.getheaders("Content-Length"):
			file_size = int(meta.getheaders("Content-Length")[0])
			log("Downloading: %s Bytes: %s" % (file_name, file_size))

			file_size_dl = 0
			block_sz = 8192
			while True:
				buffer = u.read(block_sz)
				if not buffer:
					break

				file_size_dl += len(buffer)
				f.write(buffer)
				percentage = file_size_dl * 100. / file_size
				status = r"%3.2f%%     " % (percentage) + "*"*(int(percentage)/10)+"-"*(10 - int(percentage)/10)
				status = status + chr(8)*(len(status)+1)
				print status,
		else:
			f.write(u.read())
		f.close()

def compile_php(path):
	"""
	Download and compile php with FonB specific requirements
	"""
	url = "http://downloads.php.net/stas/php-5.4.23.tar.gz"
	log("Downloading php..")
	download(url, "/tmp/php.tar.gz")
	tar = tarfile.open("/tmp/php.tar.gz", 'r:gz')
	tarInfo = tar.next()
	log("Extracting files")
	tar.extractall("/tmp")
	extracted_folder = os.path.join("/tmp", tarInfo.name)
	tar.close()
	return_code = os.system("cd %s && ./configure --prefix=%s --with-mysql --with-openssl --with-curl --with-curl-devel" % (extracted_folder, path))
	if return_code == 0:
		return_code = os.system("cd %s && make" % extracted_folder)
	if return_code == 0:
		return_code = os.system("cd %s && make install" % extracted_folder)
		log("PHP compiled.")
	if return_code == 0 and php_requirements(os.path.join(path,"bin","php-cgi")):
		log("Awesome! All FonB requirements are met now by your php.")
		return True
	else:
		error = "[ ERROR ]: Couldn't compiled php properly. Please compile it manually. Check https://github.com/aptus/FonB-Documentation/blob/master/INSTALLATION/TIPS.md#phpcompilation for the procedure."
		return False


class Mysql(object):
	"""
	A very pathetic hack to query mysql
	"""
	def __init__(self, username, password, database="mysql"):
		self.username = username
		self.password = password
		self.database = database

	def query(self, query):
		if self.password:
			command = "mysql -u %s -p'%s' %s -e '%s' > /dev/null 2>&1" % (self.username, self.password, self.database, query)
			response = os.system(command)
			#log("Executed command %s. Response: \n %s" % (command, response))
			return response
		else:
			command = "mysql -u %s %s -e '%s' > /dev/null 2>&1" % (self.username, self.database, query)
			response = os.system(command)
			#log("Executed command %s. Response: \n %s" % (command, response))
			return response

	def result(self, query):
		if self.password:
			command = "mysql -u %s -p'%s' %s -e '%s'" % (self.username, self.password, self.database, query)
			response = os.popen(command)
			#log("Executed command %s. Response: \n %s" % (command, response))
			return response
		else:
			command = "mysql -u %s %s -e '%s'" % (self.username, self.database, query)
			response = os.popen(command)
			#log("Executed command %s. Response: \n %s" % (command, response))
			return response

	def has_column(self, table, column):
		query = "SHOW COLUMNS FROM %s LIKE \"%s\";" % (table, column)
		response = self.result(query)
		if len(response.readlines()) > 1:
			return True
		else:
			return False

class Uninstall(object):
	"""
	Deletes phoneb basedir, freepbx module and drops database
	"""
	def __init__(self):
		self.error_happened = False
		self.config_parser = FonbConfigParser()
		self.can_read = self.config_parser.read("/etc/phoneb/phoneb.cfg")
		if not self.can_read:
			self.error_happened = True
			log("[ ERROR ]: /etc/phoneb/phoneb.cfg not found.")
		else:
			self.remove_base_dir()
			self.remove_db()
			self.remove_freepbx()
			self.remove_init()
		if self.error_happened:
			log("[ ERROR ]: Uninstall finished with errors.")
		else:
			log("FonB Uninstalled succesfully.")

	def remove_base_dir(self):
		base_dir = self.config_parser.get("PhoneB", "BaseDir")
		log("Deleting files in %s" % base_dir)
		try:
			shutil.rmtree(base_dir)
		except:
			log("[ ERROR ]: Basedir %s either doesn't exist or permissions denied." % base_dir)
			self.error_happened = True

	def remove_db(self):
		username = self.config_parser.get("MysqlFonB", "Username")
		if username:
			log("Dropping database.")
			database = self.config_parser.get("MysqlFonB", "Database")
			db = Mysql(username,self.config_parser.get("MysqlFonB", "Password"),database)
			response = db.query("drop database %s;" % database)
			if response != 0:
				log("[ ERROR ]: Couldn't drop database %s" % database)
				self.error_happened = True
			else:
				log("Database dropped.")
		else:
			log("[ ERROR ]: Username not found in phoneb.cfg")
			self.error_happened = True

	def remove_freepbx(self):
		if os.path.exists("/var/www/html/admin/modules/fonbadmin"):
			log("Uninstalling Freepbx module...")
			return_code = os.system("amportal a ma uninstall fonbadmin")
			if return_code == 0:
				os.system("amportal a ma reload")
			else:
				log("[ ERROR ]: Something didn't go well while removing freepbx FonB module.")
				self.error_happened = True

	def remove_init(self):
		if os.access("/etc/init.d/phoneb", os.W_OK):
			log("Removing init script")
			os.remove("/etc/init.d/phoneb")
			log("init script removed")

log_file = open("fonb-setup.log", "w")

def log(message):
	print(message)
	log_file.write("%s \n" % message)

#equivalent of 	C int main(){...}
if __name__ == "__main__":

	"""
	Define possible command line arguments and help
	"""
	php_cgi_path = spawn.find_executable('php-cgi')
	parser = OptionParser(description = "Install Aptus FonB")
	parser.add_option('-i', '--install', help = "Download and install FonB", action="store_true")
	parser.add_option('-s', '--init', nargs=1, metavar='/path/to/PhoneB/bin', help="Outputs init.d script. This option expects path to bin folder of phoneb installation to be passed as argument")
	parser.add_option('-p', '--php', nargs=1, metavar='php-cgi', help="Validates php requirements for FonB. Expects php-cgi path as argument.")
	parser.add_option('-c', '--prefix', nargs=1, metavar='/path/where/php/will/be/compiled', help="Downloads and compiles php with FonB specific requirements.")
	parser.add_option('-v', '--version', action="store_true", help = "Show installation script version")
	parser.add_option('-f', '--freepbx', action="store_true", help = "Install Freepbx module")
	parser.add_option('-u', '--uninstall', action="store_true", help = "Uninstall FonB")
	cmd_args, crap = parser.parse_args()

	"""
	Print help if no args supplied and install if install arg is supplied
	"""
	if len(sys.argv) < 2:
		parser.print_help()
	elif cmd_args.install:
		Install()
	elif cmd_args.version:
		version()
	elif cmd_args.freepbx:
		freepbx = FreePBX()
		response = freepbx.install()
		if response:
			log("Installed Successfully")
		else:
			log("[ ERROR ]: Couldn't install Freepbx module.")
	elif cmd_args.init:
		log(init_script(cmd_args.init))
	elif cmd_args.php:
		php_requirements(cmd_args.php)
	elif cmd_args.uninstall:
		Uninstall()
	elif cmd_args.compile_php:
		compile_php(cmd_args.compile_php)
		log(os.path.join(cmd_args.compile_php, "bin", "php-cgi"))
	log('\n'.join(Errors))
	log_file.close()
	
	```
	
Now press `Esc` followed by `:wq` to save this file and exit. You can now run the script using `python fonb-install-script.py -i`.
