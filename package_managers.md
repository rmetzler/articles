# Application-level Package Managers, a comparison - pros & cons

In this article I will compare this 9 Package Managers. 

- Bundler (Ruby) 
- PyPI (Python) 
- Composer (PHP)
- NPM (Node.JS) 
- Bower (JS, CSS, HTML) 
- CocoaPods (Objective-C)
- Maven (Java) 
- Gradle (Groovy) 
- Lein (Clojure) 

This are all application-level Package Managers, used by software developers. This is not a comparison of system-level Package Managers, such as apt, rpm or yum! 

I picked this 9 Package Managers (PMs) because I have experience  with them. I wrote crawlers for them and I wrote parsers for their project files. This list is of course not complete. On [Wikipedia](http://en.wikipedia.org/wiki/List_of_software_package_management_systems#Application-level_package_managers) you will find a more complete list of all application-level PMs. 

## Basics 

Package managers have the purpose to easily share and consume software libraries and of course to manage dependencies! If you are a software developer and you are still downloading software libraries with your browser and placing them into the right directory by hand, then you are doing something wrong! That is totally 1999! 

Nowadays every serious software dev is using at least 1 package manager at work. That means you define the software libraries (dependencies) you want to use in your project in a `project file`. The package manager then is responsible for 

 - Downloading the software libraries from a repository. 
 - Placing the downloaded libraries into the right place. 
 - Resolving transitive dependencies. 
 
A package manager has always 2 parts. 

 - A **Repository** of binaries or source code. 
 - A **client** which is communicating with the Repository and performing some work on the client side. 
 
 The advantage of such a system is pretty clear. 

- It's much easier to stay in control of dependencies. Updating dependencies is easy! You just change a number in a text file and execute a command. The package manager is doing the rest for you. 
- Another big advantage is that you don't need to checkin the actual software libraries into your SCM. You only checkin  the `project file` from your package manager.

## Bundler (Ruby)

[Bundler](http://bundler.io/) is the package manager for Ruby. Libraries are bundled in so called GEMs. A GEM file contains a directory with source code and a `*.gemspec` file. The `*.gemspec` file cotaines meta information to the GEM, such as name, version, authoer, license and of course the dependencies of the GEM. A GEM can be build like this: 

```
gem build rails.gemspec
```

And published to the central [GEM repository](http://rubygems.org) like this: 

```
gem push rails-1.0.0.gem
```

In an non library project the dependencies are defined in a `Gemfile`, a simple text file. Here is an example: 

```
source 'http://rubygems.org'

gem 'rails'        , '3.2.16'
gem 'jquery-rails' , '2.2.1'

group :test do
  gem 'rspec'      , '2.14.0'
end
```

The file starts with defining the repository. Which is [rubygems.org](http://rubygems.org), the central repository for all ruby GEMs. Each line in the file defines a GEM, with name and version. GEMs can be grouped together for different environments. GEMs for testing for example are not required on production, but in test environments. 

You don't need to define an exact version number. You can also define a range of versions and take advantage of pre defined operators. Everything well documented on [bundler.io](http://bundler.io/). 

Just execute this command in the directory where the `Gemfile` is placed and it will fetch all dependencies from the repository, resolves transitive dependencies and places them into the right directory. 

```
bundle install 
```

This comand also creates or updates a `Gemfile.lock`. This file contains the actually locked versions of the dependencies in the `Gemfile` + their transtive dependencies. The locked versions in the `Gemfile.lock` are specially important when you work with version ranges. The `Gemfile.lock` is something you checkin to your SCM and never change by hand! More to that on [bundler.io](http://bundler.io/). 

 
### Pros 

- All GEMs are centralised on RubyGems.org. Really all ruby open source developers are using it and pushing their GEMs to it. 
- Learning curve is very low, very easy to understand how it works. 
- It's very easy to publish new GEMs on rubygems.org. It takes less then a minute! 
- It has a very good REST JSON API. 
- Besides GEMs, a tag on a git repository can be defined as a dependency. That makes it very easy to fork & patch a software library. 
- bundler supports [semantic versioning](http://semver.org) and has an own operator which supports fetching always the newest patch/minor version of a GEM.  

### Cons 

- There are no mirrors. If RubyGems.org is down you can not fetch GEMs anymore. 
- The GEMs are not signed! That's a security problem! 
- Defining a license for a GEM is not mandatory.
- The mechanism for defining GEM dependencies (.gemspec) is different from defining dependencies for non GEM projects (Gemfile).

Bundler is one of my favourite package managers. You will not find much negative writing about it in the internet, besides the points which are listed here. 


## NPM (Node.JS)

[NPM](http://npmjs.org) is the package manager for [Node.JS](http://nodejs.org/). Libraries are stored as tgz files on the central Node.JS repository, which is [npmjs.org](http://npmjs.org). An npm package is a zipped directory with source code + a package.json file, which contains all the meta information about the package, such as name, version, description, dependencies and so on. A package can be published like this: 

```
npm publish <tarball>
```

Here is an example for a package.json file from a famous Node.JS library. I modified the file a bit to get it shorter. 

```
{
  "name": "request",
  "description": "Simplified HTTP request client.",
  "version": "2.31.1",
  "author": "Mikeal Rogers",
  "repository": {
    "type": "git",
    "url": "http://github.com/mikeal/request.git"
  },
  "bugs": {
    "url": "http://github.com/mikeal/request/issues"
  },
  "engines": [
    "node >= 0.8.0"
  ],
  "main": "index.js",
  "dependencies": {
    "qs": "~0.6.0",
    "json-stringify-safe": "~5.0.0",
    "forever-agent": "~0.5.0",
    "node-uuid": "~1.4.0",
    "mime": "~1.2.9"
  },
  "optionalDependencies": {
    "tough-cookie": "~0.10.0",
    "form-data": "~0.1.0",
    "tunnel-agent": "~0.3.0",
    "http-signature": "~0.10.0",
    "oauth-sign": "~0.3.0",
    "hawk": "~1.0.0",
    "aws-sign2": "~0.5.0"
  },
  "scripts": {
    "test": "node tests/run.js"
  }
}
``` 

By executing the following command in a directory with a `package.json` file, NPM will download all dependencies from the `package.json` file, resolve transitive dependencies and place them into the right place. 

### Pros

- All packages are centralized at npmjs.org. 
- Learning curve is very low, very easy to understand how it works. 
- It's very easy to publish new packages on npmjs.org. It takes less then a minute! 
- It has a very good REST JSON API. 
- Besides npm packages, a tag on a git repository can be defined as a dependency. That makes it very easy to fork & patch a software library. 
- NPM supports [semantic versioning](http://semver.org) and has an own operator which supports fetching always the newest patch/minor version of a package. 
- There is an [NPM mirror](http://npmjs.eu/) in Europe.  

### Cons

- The NPM packages are not signed! That's a security problem! 
- Defining a license for a GEM is not mandatory.

NPM is a very young package manager. They learned from the failures of other package managers. 


## Composer (PHP)

[Composer](http://getcomposer.org/) is the new package manager for PHP. It replaces [PEAR](http://pear.php.net/). There are still less then 1000 packages on Pear, but already more then 25K packages on [packagist.org](https://packagist.org/), the central repository for composer packages. 

Composer is similar to NPM. Dependencies are defined in a JSON file, called `composer.json`. Here is a very simple example: 

```
{
    "name": "your-vendor-name/package-name",
    "version": "1.0.0",
    "require": {
        "php": ">=5.3.0",
        "another-vendor/package": "1.*"
    }
}
```

Executing this command in a directory wiht `composer.json` will install all dependencies into your project. 

```
php composer.phar install
```

One big difference to NPM is that [packagist.org](https://packagist.org/) doesn't host any files. It is completely based on Git/Svn/Hn tags. Submitting a package to [packagist.org](https://packagist.org/) means submitting a link to a public Git, Subversion or Hn repository. [packagist.org](https://packagist.org/) expects that there is a `composer.json` in the root directory of the master branch AND that there tags on the repository. The name of the tag is the version number displayed on [packagist.org](https://packagist.org/). 


### Pros 
 
- All packages are centralised at [packagist.org](https://packagist.org/). 
- Learning curve is very low, very easy to understand how it works. 
- It's very easy to publish new packages on [packagist.org](https://packagist.org/).  You just have to submit a URL to a public Git repository. It takes less then a minute! 
- It has a very good REST JSON API.  
- It supports [semantic versioning](http://semver.org) and has an own operator which supports fetching always the newest patch/minor version of a package. 
- Composer has a very unique feature called "minimum stability", which allows to specify the minimum stability for a requested dependency. 

### Cons 

- There are no mirrors. If packagist.org is down you can not fetch packages anymore. 
- The packages are not signed! That's a security problem! 
- Defining a license for a package is not mandatory.

The Composer / Packagist project is even younger then NPM. It is a big step forward for the whole PHP community. All big PHP frameworks moved already to composer / packagist.

## PyPI (Python)

[PyPI](https://pypi.python.org/pypi) is the central repository for python packages. Depdendencies are defined in a `setup.py` file, which is pretty much pure Python code. Here is an example: 

```
from setuptools import setup
import os

setup(name='giki',
	version='0.1pre',
	description='a Git-based wiki',
	author='Adam Brenecki',
	author_email='adam@brenecki.id.au',
	url='',
	packages=['.'.join(i[0].split(os.sep))
		for i in os.walk('giki')
		if '__init__.py' in i[2]],
	install_requires=[
		'dulwich==0.8.5',
		'argparse', 'requests>=1.1.0,<1.3.0', 'colorama',
		'jinja2==2.6',
		'Werkzeug==0.8.3',
		# formatters
		'markdown2==2.0.1',
		'docutils==0.9.1',
		'textile==2.1.5',
		
	],
	extras_require = {
		'test':  [
			'nose==1.1.2',
			'WebTest==1.4.0',
		],
		'faster_markdown':  [
			'misaka==1.0.2',
		],
	},
	entry_points = {
    'console_scripts':
        ['giki = giki.cli:main'],
	},
)
```

Installing the dependencies works like this: 

```
python setup.py install
```

The packages at [PyPI](https://pypi.python.org/pypi) are hosted as "*.tar.gz" files. Each packages contains the source code and a `setup.py` file with meta informations, such as name, version and dependencies. 

With a little bit pre preparation a package can be published to PyPI with this 2 commands: 

```
python setup.py register -r PyPI
python setup.py sdist upload -r PyPI
```

### Pros 
 
- All packages are centralised at [PyPI](https://pypi.python.org/pypi). 
- Learning curve is very low, very easy to understand how it works. 
- It's easy to publish new packages on [PyPI](https://pypi.python.org/pypi). 
- There are [multiple mirrors](http://www.pypi-mirrors.org/) for fail-over scenarios. 

### Cons 

- The packages are not signed! That can lead to security issues. 
- Defining a license for a package is not mandatory.
- No build-in support for semantic versioning. 

PyPI is a robust package manager! There is room for improvements, that for sure. But the core commiters behind the project are aware of that and new features are in the pipeline. 

## CocoaPods (Objective-C)

[CocoaPods.org](http://cocoapods.org/) is the central repository for CocoaPods, a package manager for Objective-C software libraries. Mainly used by iPhone devs. The CocoaPods command line tool is implemented in Ruby and hosted on RubyGems.org. It can be installed like this: 

```
gem install cocoapods
```

Dependencies are defined in a simple text file called `Podfile`. Here a simple example: 

```
platform :ios
pod 'JSONKit',       '~> 1.4'
pod 'Reachability',  '~> 3.0.0'
```

Executing this command in the root directory will install the dependencies: 

```
pod install 
```

CocoaPods is completely relying on GitHub as backend. There is one [CocoaPods/Specs](https://github.com/CocoaPods/Specs/) repository with all Pod specifications available on CocoPods.org. Submitting a new pod package works via pull-request. Each pull-request gets reviewed and merged by a human. That doesn't scale infinity but it scales for the current size and guarantees a high quality for the pod specs.  

### Pros 
 
- All packages are centralised at [CocoaPods/Specs](https://github.com/CocoaPods/Specs/). 
- Learning curve is very low, very easy to understand how it works. 
- It's easy to publish new packages. Just send a pull-request on GitHub.  
- License information is mandatory! Pull-requests for Pods without license definition will not be merged. 
- It supports [semantic versioning](http://semver.org) and has an own operator which supports fetching always the newest patch/minor version of a package.
- Very good documentation. 

### Cons 

- The packages are not signed! That can lead to security issues. 
- No mirrors available. 

CocoaPods is a young project. The quality of the Pod specs is very high, because of the human review. If they grow to the size of RubyGems this workflow will not scale anymore. But for right now it's good enough. 

## Bower (Frontside JS & CSS)

[Bower.io](http://bower.io/) is a package manager for frontside JavaScript and CSS.  Pretty much for everything what you are loading from the server to the browser. Packages like jquery.js and bootstrap.css for example. 

The bower command line tool is available as package on NPM. It can be installed globally like this: 

```
npm install -g bower 
```

Installing a bower package works like this: 

```
bower install <package>#<version>
```

It downloads the package and his dependencies into a directory called "bower_components". 

Each bower package is described in a bower.json file. Here an example: 

```
{
  "name": "my-project",
  "version": "1.0.0",
  "main": "path/to/main.css",
  "ignore": [
    ".jshintrc",
    "**/*.txt"
  ],
  "dependencies": {
    "<name>": "<version>",
    "<name>": "<folder>",
    "<name>": "<package>"
  },
  "devDependencies": {
    "<test-framework-name>": "<version>"
  }
}
```

Bower is completely Git based and works without any user authentication. Everybody can register new packages like this: 

```
bower register <my-package-name> <git-endpoint>
```

Bower excepts that the root of the git-endpoint contains a `bower.json` file, describing the bower package. And it expects that there are some tags on the Git repository. The names of the tags are the version numbers.  

To unregister a package you have to ask the maintainers in the [longest GitHub issue in the history of mankind](https://github.com/bower/bower/issues/120).  

### Pros 
 
- All packages are centralised at [bower.io](http://bower.io/). 
- Learning curve is very low, very easy to understand how it works. 
- It's easy to publish new packages. Works even without registration.   

### Cons 

- The packages are not signed! That can lead to security issues. 
- No mirrors available. 
- The process for unregistering a package is a No-Go. 
- License informations are not mandatory. 
- Many registered packages doesn't provide a bower.json in their git repository. The package registration process doesn't check if the package is valid, if it really contains a bower.json file or if there are some tags on the repository. 

Bower is kind of cool, but the quality of the packages is many times bad. Unregistering a package is a pain. And a couple hundred registered packages doesn't provide a `bower.json` file in their repositories. User authentication and package validation would be necessary to improve that. 

## Maven (Java)

Maven is the package manager for Java. The central maven repositry is [Search.maven.org](http://search.maven.org/). A project and his dependencies are defined in an `pom.xml` file. Here is an example. 

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

	<modelVersion>4.0.0</modelVersion>
	<groupId>org.ploin.pmf</groupId>
	<artifactId>ploinMailFactory</artifactId>
	<packaging>jar</packaging>
	<version>1.4.1</version>
	<name>ploinMailFactory</name>
	<url>http://www.ploinmailfactory.org</url>
	<description>Loadbalancing for Mail Sending</description>
	
	<dependencies>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>${junitVersion}</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>commons-logging</groupId>
			<artifactId>commons-logging</artifactId>
			<version>1.1</version>
			<scope>compile</scope>
		</dependency>
	</dependencies>

</project>

```

Each dependency is identified by: 

- groupId
- artifactId
- verison

Other properties like scope are optional. As you can see you need at least 5 lines to define 1 single dependency. Dependencies are checked, fetched, resolved and installed on each compile time. 

```
mvn compile 
```

The packages on the Maven Repository Server are pretty much maintained in a pre defined directory structure, based on groupId, artifactId and version of the artifact. The artifacts themselves are binaries, mostly `*.jar`, ` files. You can see it on this mirror from [ibiblio](http://mirrors.ibiblio.org/maven2/). 

Publishing an artifact on the central maven repositry takes only 1 week. No! I'm not kidding you! First of all you have to signup at JIRA and then you have to open a ticket and upload files. The whole prozess is described [here](https://docs.sonatype.org/display/Repository/Sonatype+OSS+Maven+Repository+Usage+Guide). And be prepaired to scroll. 

### Pros 
 
- The artifacts are singed! 
- There are many mirrors available all over the world. 

### Cons 
 
- Pushing artifacts to the maven central repository is not as easy as it should be. Because this prozess is a pain in the ass ALL big Java Frameworks and companies are maintaining their own Maven Repository Servers. They push their artifacts first to their own MVN Server and then 1 or 2 weeks later the artifact appears on search.maven.org. 
- Not really centralised. Because of the previous point. There are at least 30 different public maven repository servers out there, who are not mirroring search.maven.org. Java developers have to google to find the right repository server with the desired artifacts. 
- Maven is a very complex tool! A `pom.xml` file can inherit from another `pom.xml` file. And a `pom.xml` file can include other `pom.xml` files. All that leads to complexity and sometimes to resolution errors. 
- Maven violates the "Single Responsibility" pattern. Maven is not only doing dependecy management! It is doing all kind of things which has nothing to do with dependency management. For example executing tests, generating reports, generating JavaDoc and many other things which are, in other languages, done by other tools. 

Maven is by far the most complex package manager I know. And know many of them! 













 







