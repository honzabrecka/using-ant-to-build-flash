Using ANT to build flash
========================

A collection of examples of using ANT to automate building flash apps/games. It's meant to be a cook book or a learning resource, therefore
each example is as small as possible and should be self-describing. 

Make your build continuous integration ready!

Introduction
------------

### Environment

#### Java

At first of all, you have to install [JRE](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) (Java Runtime Environment). To check everything works as expected, just type `$ java` to your terminal.

#### ANT

Then you have to download ANT from http://ant.apache.org/bindownload.cgi. Just extract the downloaded .zip file into the, let's say `~/sdks/ant` directory, set `ANT_HOME` environment variable to this location and add `ANT_HOME` to `PATH`:

`${ANT_HOME}/bin` (MacOS) or `%ANT_HOME%/bin` (Windows)

To check everything works as expected, just type `$ ant` to your terminal.

> If you are using MacOS you can install ANT with [brew](http://brew.sh/): `$ brew install ant`

#### AIR SDK

If you are using Adobe Flash Builder 4.7, you can find the AIR SDK at `/Applications/Adobe Flash Builder 4.7/eclipse/plugins/com.adobe.flash.compiler_4.7.0.349722` (MacOS) or `C:\Program Files (x86)\Adobe\Adobe Flash Builder 4.7\eclipse\plugins\com.adobe.flash.compiler_4.7.0.349722` (Windows).

Otherwise, you can download it from http://www.adobe.com/devnet/air/air-sdk-download.html. Just extract it into the, let's say `~/sdks/air` directory.

#### FLEX SDK

If you are using Adobe Flash Builder 4.7, you can find the Flex SDKs directory at `<FlashBuilder_installation_location>/sdks`.

Otherwise, you have to download and install it manually. I personally prefer release by Adobe, which can be downloaded from http://www.adobe.com/devnet/flex/flex-sdk-download.html. Just extract it into the, let's say `~/sdks/flex` directory. But you may want to use the latest version of the Flex SDK, which is currently developed by Apache. If so, you can install it using their http://flex.apache.org/installer.html.

You should set the `FLEX_HOME` environment variable and add it to `PATH`.

### Compilers and tools

#### mxmlc

You use the application compiler to compile SWF files from your ActionScript and/or MXML source files.

 - [Using mxmlc, the application compiler](http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7fcc.html)
 - [compiler options](http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7a92.html)

#### compc

You use the component compiler to generate a SWC file from component source files and other asset files such as images and style sheets.

 - [Using compc, the component compiler](http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7fd2.html)
 - [About the component compiler options](http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7a80.html)

#### amxmlc

You can compile the ActionScript and MXML assets of your AIR application with the command line MXML compiler (amxmlc).

 - [Compiling an AIR application with the amxmlc compiler](http://help.adobe.com/en_US/AIR/1.5/devappsflex/WS5b3ccc516d4fbf351e63e3d118666ade46-7fa1.html)

#### acompc

Use the component compiler, acompc, to compile AIR libraries and independent components.

 - [Compiling an AIR component or library with the acompc compiler](http://help.adobe.com/en_US/AIR/1.5/devappsflex/WS5b3ccc516d4fbf351e63e3d118666ade46-7f60.html)

#### ASDoc

[ASDoc](http://help.adobe.com/en_US/flex/using/WSd0ded3821e0d52fe1e63e3d11c2f44bb7b-7fe7.html) is a command-line tool that you can use to create API language reference documentation as HTML pages from the ActionScript classes and MXML files.

#### ADT

The [AIR Developer Tool](http://help.adobe.com/en_US/air/build/WS5b3ccc516d4fbf351e63e3d118666ade46-7fd9.html) is a multi-purpose, command-line tool for developing AIR applications. You can use ADT to perform the following tasks:

 - Package an AIR application as an .air installation file
 - Package an AIR application as a native installer—for example, as a .exe installer file on Windows, .ipa on iOS, or .apk on Android
 - Package a native extension as an AIR Native Extension (ANE) file
 - Sign an AIR application with a digital certificate
 - Change (migrate) the digital signature used for application updates
 - Determine the devices connected to a computer
 - Create a self-signed digital code signing certificate
 - Remotely install, launch, and uninstall an application on a mobile device
 - Remotely install and uninstall the AIR runtime on a mobile device

#### ADL

Use the [AIR Debug Launcher](http://help.adobe.com/en_US/AIR/1.5/devappshtml/WS5b3ccc516d4fbf351e63e3d118666ade46-7fd7.html) to run both SWF-based and HTML-based applications during development. Using ADL, you can run an application without first packaging and installing it. By default, ADL uses a runtime included with the SDK, which means you do not have to install the runtime separately to use ADL.


Building SWC
------------

#### Example 1 - basics

The most simple ANT build.xml file, which describes how to build an `bin/output.swc` file:

```
<?xml version="1.0"?>
<project name="swc example" default="main" basedir=".">
	<taskdef resource="flexTasks.tasks" classpath="${FLEX_HOME}/ant"/>
	<property name="DEPLOY.dir" value="${basedir}/bin"/>
	<target name="main" depends="clean, compile"/>
	<target name="clean">
		<delete dir="${DEPLOY.dir}"/>
		<mkdir dir="${DEPLOY.dir}"/>
	</target>
	<target name="compile">
	<compc output="${DEPLOY.dir}/output.swc" failonerror="true" maxmemory="1024m">
		<source-path path-element="${basedir}/src"/>
		<include-sources dir="${basedir}/src" includes="*"/>
	</compc>
</target>
</project>
```

#### Example 2 - linking libs directory

Let's say, that the project contains a `libs` directory with linked .swc libraries. We have to tell to compc where to find them:

```
<?xml version="1.0"?>
<project name="swc example" default="main" basedir=".">
	<taskdef resource="flexTasks.tasks" classpath="${FLEX_HOME}/ant"/>
	<property name="DEPLOY.dir" value="${basedir}/bin"/>
	<target name="main" depends="clean, compile"/>
	<target name="clean">
		<delete dir="${DEPLOY.dir}"/>
		<mkdir dir="${DEPLOY.dir}"/>
	</target>
	<target name="compile">
	<compc output="${DEPLOY.dir}/output.swc" failonerror="true" maxmemory="1024m">
		<source-path path-element="${basedir}/src"/>
		<include-sources dir="${basedir}/src" includes="*"/>
		<library-path dir="${basedir}/libs" includes="*" append="true"/>
	</compc>
</target>
</project>
```

#### Example 3 - linking another swc file

Or if we want to specify an .swc file located anywhere:

```
<?xml version="1.0"?>
<project name="swc example" default="main" basedir=".">
	<taskdef resource="flexTasks.tasks" classpath="${FLEX_HOME}/ant"/>
	<property name="DEPLOY.dir" value="${basedir}/bin"/>
	<target name="main" depends="clean, compile"/>
	<target name="clean">
		<delete dir="${DEPLOY.dir}"/>
		<mkdir dir="${DEPLOY.dir}"/>
	</target>
	<target name="compile">
	<compc output="${DEPLOY.dir}/output.swc" failonerror="true" maxmemory="1024m">
		<source-path path-element="${basedir}/src"/>
		<include-sources dir="${basedir}/src" includes="*"/>
		<library-path file="lib.swc" append="true"/>
	</compc>
</target>
</project>
```

#### Example 4 - custom metadata

Now we are using custom metadata and we want to keep them in compiled application (the default behavior is that they are removed them to keep the application/library as small as possible):

```
<?xml version="1.0"?>
<project name="swc example" default="main" basedir=".">
	<taskdef resource="flexTasks.tasks" classpath="${FLEX_HOME}/ant"/>
	<property name="DEPLOY.dir" value="${basedir}/bin"/>
	<target name="main" depends="clean, compile"/>
	<target name="clean">
		<delete dir="${DEPLOY.dir}"/>
		<mkdir dir="${DEPLOY.dir}"/>
	</target>
	<target name="compile">
	<compc output="${DEPLOY.dir}/output.swc" failonerror="true" maxmemory="1024m">
		<source-path path-element="${basedir}/src"/>
		<include-sources dir="${basedir}/src" includes="*"/>
		<library-path dir="${basedir}/libs" includes="*" append="true"/>
		<keep-as3-metadata name="Inject"/>
		<keep-as3-metadata name="PostConstruct"/>
		<keep-as3-metadata name="ArrayElementType"/>
	</compc>
</target>
</project>
```

#### Example 5 - changing target player version

```
<compc ... target-player="11.4" swf-version="17"/>
```

Do not forget to check if `FLEX_HOME/frameworks/libs/player/<target-version>/playerglobal.swc` and `${FLEX_HOME}/runtimes/player/<target-version>/.../FlashPlayerDebugger` exist. You can find all released versions of playerglobal.swc and Flash Player Debuggers at http://helpx.adobe.com/flash-player/kb/archived-flash-player-versions.html.

swf-version | target-player
----------- | -------------
9           | 9
10          | 10.0, 10.1
11          | 10.2
12          | 10.3
13          | 11.0
14          | 11.1
15          | 11.2
16          | 11.3
17          | 11.4
18          | 11.5
19          | 11.6
20          | 11.7
21          | 11.8
22          | 11.9
23          | 12.0
24          | 13.0
25          | 14.0

#### Example 6 - dump details about compilation

Let's take a look at the compc options. If you are intrested in more detailed informations, you can dump out config, size report and link report:

```
<compc ... dump-config="${DEPLOY.dir}/config.xml" size-report="${DEPLOY.dir}/sizereport.xml" link-report="${DEPLOY.dir}/linkreport.xml">
```

#### Example 7 - disable warnings

To disable warnings:

```
<compc ... warnings="false">
```

#### Example 8 - exhausted memory error

If you are building a lot of applications/libraries, you can exhaust the memory. Very useful is set fork option to true, which should solve, especially with maxmemory set to at least one gig, everything:

```
<compc ... fork="true">
```

Building SWF from Flex project
------------------------------

#### Example 9 - basics

The most simple ANT build.xml file, which describes how to build a `bin/output.swf` file:

```
<?xml version="1.0"?>
<project name="swf example" default="main" basedir=".">
	<taskdef resource="flexTasks.tasks" classpath="${FLEX_HOME}/ant"/>
	<property name="DEPLOY.dir" value="${basedir}/bin"/>
	<target name="main" depends="clean, compile"/>
	<target name="clean">
		<delete dir="${DEPLOY.dir}"/>
		<mkdir dir="${DEPLOY.dir}"/>
	</target>
	<target name="compile">
		<mxmlc file="${basedir}/src/Main.mxml" output="${DEPLOY.dir}/output.swf" failonerror="true" maxmemory="1024m">
			<source-path path-element="${basedir}/src"/>
		</mxmlc>
	</target>
</project>
```

> The mxmlc compiler offers almost the same options as the compc compiler does. Each example described in [Building SWC](#building-swc) section can be easily changed to mxmlc command. Compare [Example 1](#example-1---basics) with [Example 9](#example-9---basics) and you'll see.

Building SWF from pure ActionScript project
-------------------------------------------

#### Example 10 - basics

The most simple ANT build.xml file, which describes how to build an `bin/output.swf` file:

```
<?xml version="1.0"?>
<project name="swf example" default="main" basedir=".">
	<taskdef resource="flexTasks.tasks" classpath="${FLEX_HOME}/ant"/>
	<property name="DEPLOY.dir" value="${basedir}/bin"/>
	<target name="main" depends="clean, compile"/>
	<target name="clean">
		<delete dir="${DEPLOY.dir}"/>
		<mkdir dir="${DEPLOY.dir}"/>
	</target>
	<target name="compile">
		<mxmlc
			file="${basedir}/src/Main.as"
			output="${DEPLOY.dir}/output.swf"
			static-link-runtime-shared-libraries="true"
			failonerror="true"
			maxmemory="1024m">
			<source-path path-element="${basedir}/src"/>
			<source-path path-element="${FLEX_HOME}/frameworks"/>
		</mxmlc>
	</target>
</project>
```

> The mxmlc compiler offers almost the same options as the compc compiler does. Each example described in [Building SWC](#building-swc) section can be easily changed to mxmlc command. Compare [Example 1](#example-1---basics) with [Example 10](#example-10---basics) and you'll see.

Building AIR project
--------------------

#### Example 11 - basics

The most simple ANT build.xml file, which describes how to build an `bin/output.swf` file:

```
<?xml version="1.0"?>
<project name="swf example" default="main" basedir=".">
	<taskdef resource="flexTasks.tasks" classpath="${FLEX_HOME}/ant"/>
	<property name="DEPLOY.dir" value="${basedir}/bin"/>
	<target name="main" depends="clean, compile"/>
	<target name="clean">
		<delete dir="${DEPLOY.dir}"/>
		<mkdir dir="${DEPLOY.dir}"/>
	</target>
	<target name="compile">
		<mxmlc file="${basedir}/src/Main.as" output="${DEPLOY.dir>/output.swf" configname="air" failonerror="true">
			<library-path dir="${FLEX_HOME}" append="true">
				<include name="frameworks/libs"/>
			</library-path>
			<library-path dir="${FLEX_HOME}" append="true">
				<include name="frameworks/libs/air"/>
			</library-path>
		</mxmlc>
	</target>
</project>
```

> The mxmlc compiler offers almost the same options as the compc compiler does. Each example described in [Building SWC](#building-swc) section can be easily changed to mxmlc command. Compare [Example 1](#example-1---basics) with [Example 11](#example-11---basics) and you'll see.

Running FlexUnit tests in FlashPlayer
--------------------------------------

> Quick download links: [Apache FlexUnit 4.2](http://www.apache.org/dyn/closer.cgi/flex/flexunit/4.2.0/binaries/apache-flex-flexunit-4.2.0-4.12.0-bin.zip), [FlexUnit 4.1](https://dl.dropboxusercontent.com/u/75480155/flexunit.zip)

#### Example 12 - running FlexUnit tests in FlashPlayer

> Do not forget to add `CIListener` to your `FlexUnitCore` instance.

```
<?xml version="1.0"?>
<project name="swf example" default="main" basedir=".">
	<taskdef resource="flexTasks.tasks" classpath="${FLEX_HOME}/ant"/>
	<taskdef resource="flexUnitTasks.tasks">
		<classpath>
			<fileset dir="<path_to_flex_unit_ant_tasks_dir>">
				<include name="flexUnitTasks*.jar"/>
			</fileset>
		</classpath>
	</taskdef>
	<property name="DEPLOY.dir" value="${basedir}/bin"/>
	<property name="TESTS.dir" value="${basedir}/tests-output"/>
	<target name="main" depends="clean, tests, compile"/>
	<target name="clean">
		<delete dir="${DEPLOY.dir}"/>
		<mkdir dir="${DEPLOY.dir}"/>
		<delete dir="${TESTS.dir}"/>
		<mkdir dir="${TESTS.dir}"/>
	</target>
	<target name="tests">
		<mxmlc file="${basedir}/src/tests.mxml" output="${TESTS.dir}/tests.swf" failonerror="true" verbose-stacktraces="true">
			<source-path path-element="${basedir}/src"/>
			<library-path dir="${basedir}/libs" includes="*" append="true"/>
		</mxmlc>
		<flexunit
			swf="${TESTS.dir}/tests.swf"
			toDir="${TESTS.dir}"
			haltonfailure="false" failureproperty="flexunit.failure"
			verbose="true"
			localTrusted="true"
			command="${FLEX_HOME}/runtimes/player/11.4/win/FlashPlayerDebugger.exe"/>
		<junitreport todir="${TESTS.dir}">
			<fileset dir="${TESTS.dir}">
				<include name="TEST-*.xml"/>
			</fileset>
			<report format="frames" todir="${TESTS.dir}/html"/>
		</junitreport>
	</target>
	<target name="compile">
		<mxmlc file="${basedir}/src/Main.mxml" output="${DEPLOY.dir}/output.swf" failonerror="true" maxmemory="1024m">
			<source-path path-element="${basedir}/src"/>
		</mxmlc>
	</target>
</project>
```

Running FlexUnit tests in ADL
-----------------------------

#### Example 13 - running FlexUnit tests in ADL

> Do not forget to add `AIRCIListener` to your `FlexUnitCore` instance.

```
<?xml version="1.0"?>
<project name="swf example" default="main" basedir=".">
	<taskdef resource="flexTasks.tasks" classpath="${FLEX_HOME}/ant"/>
	<taskdef resource="flexUnitTasks.tasks">
		<classpath>
			<fileset dir="<path_to_flex_unit_ant_tasks_dir>">
				<include name="flexUnitTasks*.jar"/>
			</fileset>
		</classpath>
	</taskdef>
	<property name="DEPLOY.dir" value="${basedir}/bin"/>
	<property name="TESTS.dir" value="${basedir}/tests-output"/>
	<target name="main" depends="clean, tests, compile"/>
	<target name="clean">
		<delete dir="${DEPLOY.dir}"/>
		<mkdir dir="${DEPLOY.dir}"/>
		<delete dir="${TESTS.dir}"/>
		<mkdir dir="${TESTS.dir}"/>
	</target>
	<target name="test">
		<mxmlc file="${basedir}/src/tests.as" output="${TESTS.dir}/tests.swf" configname="air" failonerror="true" verbose-stacktraces="true">
			<library-path dir="${basedir}/libs"includes="*"append="true"/>
			<library-path dir="${FLEX_HOME}"append="true">
				<include name="frameworks/libs"/>
			</library-path>
			<library-path dir="${FLEX_HOME}" append="true">
				<include name="frameworks/libs/air"/>
			</library-path>
		</mxmlc>
		<copy file="${basedir}/src/tests-app.xml" todir="${TESTS.dir}" overwrite="true"/>
		<replace file="${TESTS.dir}/tests-app.xml" token="[This value will be overwritten by Flash Builder in the output app.xml]" value="tests.swf"/>
		<flexunit
			player="air"
			swf="${TESTS.dir}/tests.swf"
			toDir="${TESTS.dir}" 
			haltonfailure="false" failureproperty="flexunit.failure"
			verbose="true"/>
		<junitreport todir="${TESTS.dir}">
			<fileset dir="${TESTS.dir}">
				<include name="TEST-*.xml"/>
			</fileset>
			<report format="frames" todir="${TESTS.dir}/html"/>
		</junitreport>
	</target>
	<target name="compile">
		<mxmlc file="${basedir}/src/Main.as" output="${DEPLOY.dir>/output.swf" configname="air" failonerror="true">
			<library-path dir="${FLEX_HOME}" append="true">
				<include name="frameworks/libs"/>
			</library-path>
			<library-path dir="${FLEX_HOME}" append="true">
				<include name="frameworks/libs/air"/>
			</library-path>
		</mxmlc>
	</target>
</project>
```

Packing AIR project
-------------------

#### Example 14 - packing .air application

```
...
<target name="pack">
	<copy file="${basedir}/src/swf2png-app.xml" todir="${DEPLOY.dir}" overwrite="true"/>
	<replace file="${DEPLOY.dir}/swf2png-app.xml" token="[This value will be overwritten by Flash Builder in the output app.xml]" value="swf2png.swf"/>
	<exec executable="${FLEX_HOME}/bin/adt.bat" failonerror="true">
		<arg line="-package"/>
		<arg line="-storetype pkcs12"/>
		<arg line="-keystore ${basedir}/certificate.p12"/>
		<arg line="-storepass <password>"/>
		<arg line="${DEPLOY.dir}/swf2png.air"/>
		<arg line="${DEPLOY.dir}/swf2png-app.xml"/>
		<arg line="-C ${DEPLOY.dir} swf2png.swf"/>
	</exec>
</target>
...
```

Generating ASDoc
----------------

#### Example 15 - generating ASDoc

Generate ASDoc to a temp dir and update the .swc file with it.

```
...
<target name="asdoc">
	<tempfile property="temp.dir" destDir="${java.io.tmpdir}" prefix="${ant.project.name}-doc-xml-" />
	<asdoc output="${temp.dir}" lenient="true" failonerror="true" keep-xml="true" skip-xsl="true" fork="true">
		<compiler.source-path path-element="${basedir}/src" />
		<doc-sources path-element="${basedir}/src" />
	</asdoc>
	<zip destfile="${DEPLOY.dir}/${ant.project.name}.swc" update="true">
		<zipfileset dir="${temp.dir}/tempdita" prefix="docs">
			<include name="*.*"/>
			<exclude name="ASDoc_Config.xml" />
			<exclude name="overviews.xml" />
		</zipfileset>
	</zip>
	<delete dir="${temp.dir}" failonerror="false" includeEmptyDirs="true" />
</target>
...
```

FlexPMD/CPD
-----------

[FlexPMD](http://sourceforge.net/adobe/flexpmd/home/Home/) is a source code analyzer. It finds common programming flaws like unused variables, empty catch blocks, unnecessary object creation, and so forth.

To generate a `pmd_ruleset.xml` use the online [pmd ruleset creator](http://opensource.adobe.com/svn/opensource/flexpmd/bin/flex-pmd-ruleset-creator.html).

> Quick download link: [Adobe FlexPMD 1.3](https://dl.dropboxusercontent.com/u/75480155/flexpmd.zip)

#### Example 16 - analyze source code with PMD and CPD

```
...
<property name="FLEXPMD.dir" value="~/sdks/flexpmd"/>
<property name="FLEXPMD.version" value="1.3"/>
<taskdef name="flexPmd"
    classname="com.adobe.ac.pmd.ant.FlexPmdAntTask"
    classpath="${FLEXPMD.dir}/flex-pmd-ant-task-${FLEXPMD.version}.jar">
    <classpath>
        <pathelement location="${FLEXPMD.dir}/flex-pmd-ruleset-api-${FLEXPMD.version}.jar"/>
        <pathelement location="${FLEXPMD.dir}/flex-pmd-ruleset-${FLEXPMD.version}.jar"/>
        <pathelement location="${FLEXPMD.dir}/flex-pmd-core-${FLEXPMD.version}.jar"/>
        <pathelement location="${FLEXPMD.dir}/as3-plugin-utils-${FLEXPMD.version}.jar"/>
        <pathelement location="${FLEXPMD.dir}/as3-parser-api-${FLEXPMD.version}.jar"/>
        <pathelement location="${FLEXPMD.dir}/as3-parser-${FLEXPMD.version}.jar"/>
        <pathelement location="${FLEXPMD.dir}/pmd-4.2.5.jar"/>
        <pathelement location="${FLEXPMD.dir}/commons-lang-2.4.jar"/>
        <pathelement location="${FLEXPMD.dir}/flex-pmd-files-${FLEXPMD.version}.jar"/>
        <pathelement location="${FLEXPMD.dir}/flex-pmd-metrics-${FLEXPMD.version}.jar"/>
        <pathelement location="${FLEXPMD.dir}/plexus-utils-1.0.2.jar"/>
        <pathelement location="${flexpmd.libs}/dom4j-1.6.1.jar"/>
    </classpath>
</taskdef>
<taskdef name="flexCpd"
    classname="com.adobe.ac.cpd.ant.FlexCpdAntTask"
    classpath="${FLEXPMD.dir}/flex-pmd-cpd-ant-task-${FLEXPMD.version}.jar">
    <classpath>
        <pathelement location="${FLEXPMD.dir}/flex-pmd-files-${FLEXPMD.version}.jar"/>
        <pathelement location="${FLEXPMD.dir}/flex-pmd-cpd-${FLEXPMD.version}.jar"/>
        <pathelement location="${FLEXPMD.dir}/as3-plugin-utils-${FLEXPMD.version}.jar"/>
        <pathelement location="${FLEXPMD.dir}/as3-parser-api-${FLEXPMD.version}.jar"/>
        <pathelement location="${FLEXPMD.dir}/as3-parser-${FLEXPMD.version}.jar"/>
        <pathelement location="${FLEXPMD.dir}/pmd-4.2.5.jar"/>
    </classpath>
</taskdef>
...
<target name="flexPmd">
    <flexPmd
        sourceDirectory="${basedir}"
        outputDirectory="${DEPLOY.dir}"
        ruleSet="${basedir}/pmd_ruleset.xml"/>
</target>
<target name="flexCpd">
    <flexCpd
        minimumTokenCount="50"
        outputFile="${DEPLOY.dir}/cpd.xml">
        <fileset dir="${basedir}">
            <include name="**/*.as"/>
            <include name="**/*.mxml"/>
            <exclude name="vendor/**" />
        </fileset>
    </flexCpd>
</target>
...
```

Mastering
---------

Do not repeat yourself, use `${ant.project.name}` as can be seen in *Example 15 - generating ASDoc*.

When you run your FlexUnit tests on CI machine and some test fails, your build ends. But it ends before the report generation, so your statistics can't be updated and you don't know which test fails. Therefore flexunit task offers `failureproperty`:

#### Example 17 - do not halt FlexUnit tests on failure

```
...
<target name="test">
	<mxmlc file="${basedir}/src/tests.mxml" output="${TESTS.dir}/tests.swf" failonerror="true" verbose-stacktraces="true">
		<source-path path-element="${basedir}/src"/>
		<library-path dir="${basedir}/libs" includes="*" append="true"/>
	</mxmlc>
	<flexunit
		swf="${TESTS.dir}/tests.swf"
		toDir="${TESTS.dir}"
		haltonfailure="false" failureproperty="flexunit.failure"
		verbose="true"
		localTrusted="true"
		command="${FLEX_HOME}/runtimes/player/11.4/win/FlashPlayerDebugger.exe"/>
	<junitreport todir="${TESTS.dir}">
		<fileset dir="${TESTS.dir}">
			<include name="TEST-*.xml"/>
		</fileset>
		<report format="frames" todir="${TESTS.dir}/html"/>
	</junitreport>
	<fail if="flexunit.failure" message="Unit test(s) failed. See reports!"/>
</target>
...
```

License
-------

This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/">Creative Commons Attribution-NonCommercial 4.0 International License</a>
