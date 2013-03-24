### Note: 

This project is not actively maintained. 
If you would like to help maintaining it, please send an email to jakewins [at] gmail.com, or, even better, send a pull request with some fixes and a note that you'd be interested in getting commit access.

# Brew maven plugin

Build javascript apps (optionally using coffeescript) using the CommonJS Asynchronous Module Definition (AMD) 
pattern to define classes and dependencies between them. See:

http://wiki.commonjs.org/wiki/Modules/AsynchronousDefinition

Brew includes:

 * The require.js "optimizer", which aggregates and minifies AMD-based projects
 * The coffeescript compiler
 * The haml-js compiler

### Purpose

Allow building modern javascript applications within organizations that are already invested in
maven-based build systems and surrounding architecture.

### Usage

Just add the plugin to your pom:

    <plugins> 
      ..
      <plugin>
        <groupId>com.voltvoodoo</groupId>
        <artifactId>brew</artifactId>
        <version>0.2.1</version>
        <executions>
          <execution>
            <goals>
              <goal>compile</goal>
              <goal>optimize</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>

### Goal: compile

compile is by default attached to the "process-classes" phase.

compile will by default look for coffeescript files (\*.coffee) and haml files (\*.haml)
recursively in ${basedir}/src/main/coffeescript, and output the compiled js files 
into ${project.build.outputDirectory}.

You can make the compile goal watch for changes and new coffeescript and haml files, and
recompile them on the fly. That way, you don't have to re-run your maven build over and over.

To watch for changes, either set "brew.watch" to true:

    mvn brew:compile -Dbrew.watch=true

Or set the "watch" config property to true. Here are the config settings available for the 
compile goal:
  
    <plugins> 
      ..
      <plugin>
        <groupId>com.voltvoodoo</groupId>
        <artifactId>brew</artifactId>
        <version>0.1</version>
        <executions>
          <execution>
            <goals>
              <goal>compile</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <coffeeSourceDir>${basedir}/my/awesome/directory</coffeeSourceDir>
          <coffeeOutputDir>${basedir}/src/main/webapp/js</coffeeOutputDir>
          <hamlSourceDir>${basedir}/my/awesome/directory</hamlSourceDir>
          <hamlOutputDir>${basedir}/src/main/webapp/js</hamlOutputDir>
          <!-- Set this to do auto-compilation when a file is changed <watch>true</watch> -->
        </configuration>
      </plugin>
    </plugins>
    
### Goal: optimize

Uses the require.js optimizer to aggregate and minify your project. Dependencies should be 
defined using the CommonJS Asynchronous Module Definition pattern, see the require.js documentation.

The "optimize" goal is by default attached to the "process-classes" maven phase. It will go through
all the js modules you have listed in your plugin configuration, interpret the AMD dependencies
each file has, aggregate and minify that entire dependency tree, and put the resulting minified filed in 
your output directory.

By default, optimize looks for a js file named "main.js" in 
${project.build.outputDirectory}, and puts the resulting main.min.js aggregate
file in ${project.build.outputDirectory}.

The source and output directories can be set in your POM:

    <configuration>
      <!-- Find JS files to work with here -->
      <optimizeSourceDir>${basedir}/my/awesome/directory</optimizeSourceDir>
      
      <!-- Copy optimizeSourceDir to here, minify all js and css found -->
      <optimizeBuildDir>${basedir}/target/somebuildfolder</optimizeBuildDir>
      
      <!-- Copy minified js modules to here -->
      <optimizeOutputDir>${basedir}/somewhere</optimizeOutputDir>
    </configuration>

You can change the module name from the default ("main") by setting the
"module" config property. You can also provide a list of modules using the
"modules" config property, the modules setting overrides the module setting.

Minimum settings for the "modules" property would be:

    <configuration>
      ..
      <modules>
        <module>
          <name>myapp</name> <!-- Will lead to looking for a file named "myapp.js" -->
        </module>
        ..
      </modules>
    </configuration>

Brew supports almost all settings that the require.js optimization
tool does. Unfortunately, I haven't had time to document them properely.

Until they are, please refer to the java-doc in com.voltvoodoo.brew.OptimizeMojo
and in com.voltvoodoo.brew.Module
