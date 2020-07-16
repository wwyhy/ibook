<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [list all jobs](#list-all-jobs)
  - [list all Abstract Project](#list-all-abstract-project)
  - [list all jobs and folders](#list-all-jobs-and-folders)
  - [get name and classes](#get-name-and-classes)
  - [find all disabled projects/jobs](#find-all-disabled-projectsjobs)
- [shelve jobs](#shelve-jobs)
- [run shell scripts in a cluster-operation](#run-shell-scripts-in-a-cluster-operation)
- [List plugins](#list-plugins)
  - [using api (`curl`)](#using-api-curl)
  - [using cli](#using-cli)
  - [simple list](#simple-list)
  - [List plugin and dependencies](#list-plugin-and-dependencies)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## [list all jobs](https://support.cloudbees.com/hc/en-us/articles/226941767-Groovy-to-list-all-jobs)
### list all Abstract Project
> Abstract Project: freestyle, maven, etc...

```groovy
Jenkins.instance.getAllItems(AbstractProject.class).each {
  println it.fullName;
}
```

### list all jobs and folders
```groovy
Jenkins.instance.getAllItems(AbstractItem.class).each {
  println(it.fullName)
};
```

### get name and classes
```groovy
Jenkins.instance.getAllItems(Job.class).each {
  println it.name + " - " + it.class
}
```

### find all disabled projects/jobs
```groovy
jenkins.model.Jenkins.instance.getAllItems(jenkins.model.ParameterizedJobMixIn.ParameterizedJob.class).findAll{ it -> it.disabled }.each {
  println it.fullName;
}
```

## [shelve jobs](https://support.cloudbees.com/hc/en-us/articles/236353928-Groovy-Scripts-To-Shelve-Jobs)
```groovy
//You have to install the Shelve Project Plugin on your Jenkins Master
//The maximum value for daysBack is 365, going beyond 365 will break the script.

import org.jvnet.hudson.plugins.shelveproject.ShelveProjectTask

def daysBack=365;
Jenkins.instance.getAllItems(AbstractProject.class).each{ it->
  def lastBuild=it.getLastBuild()
  if(lastBuild != null){
    def back = Calendar.getInstance()
    back.set(Calendar.DAY_OF_YEAR,back.get(Calendar.DAY_OF_YEAR)-daysBack)
    if (lastBuild.getTime().compareTo(back.getTime()) < 0) {
      println it.name + " was built over " + daysBack + " days ago: " + lastBuild.getTime()
      if (it instanceof AbstractProject){
      def spt=  new ShelveProjectTask(it)
        Hudson.getInstance().getQueue().schedule(spt , 0 );
      }else{
        println it.name + " was not shelved----------- "
      }
    }
  }
}
```

## [run shell scripts in a cluster-operation](https://support.cloudbees.com/hc/en-us/articles/360020737392-How-to-run-shell-scripts-in-a-cluster-operation)
```groovy
def exec(cmd) {
  println cmd
  def process = new ProcessBuilder([ "sh", "-c", cmd])
                                    .directory(new File("/tmp"))
                                    .redirectErrorStream(true)
                                    .start()
  process.outputStream.close()
  process.inputStream.eachLine {println it}
  process.waitFor();
  return process.exitValue()
}

[
  "echo hello world",
  "ls -al"
].each {
  exec(it)
}
```

## List plugins

### [using api (`curl`)](https://stackoverflow.com/a/52836951/2940319)
```bash
$ curl -u<username>:<password> \
       -s https://<JENKINS_DOMAIN_NAME>/pluginManager/api/json?depth=1 \
       | jq -r '.plugins[] | "\(.shortName):\(.version)"' \
       | sort
```

### [using cli](https://stackoverflow.com/a/44979051/2940319)

```bash
$ cat plugin.groovy
def plugins = jenkins.model.Jenkins.instance.getPluginManager().getPlugins()
plugins.each {println "${it.getShortName()}: ${it.getVersion()}"}
```

- by `jar`

```bash
$ curl -fsSL -O https://JENKINS_URL/jnlpJars/jenkins-cli.jar
$ java -jar jenkins-cli.jar \
    [-auth <username>:<password>] \
    -s https://JENKINS_URL groovy = < plugin.groovy
```

  OR
  ```bash
  $ java -jar jenkins-cli.jar \
      [-auth <username>:<password>] \
      -s https://JENKINS_URL \
      list-plugins
  ```

- [by `ssh`](https://www.jenkins.io/doc/book/managing/cli/)

```bash
$ ssh [-i <private-key>] [-l <user>] -p <port> JENKINS_URL groovy =< plugin.groovy
```

  OR
  ```bah
  $ ssh [-i <private-key>] [-l <user>] -p <port> JENKINS_URL list-plugins
  ```

### [simple list]()
```groovy
Jenkins.instance.pluginManager.plugins.each{
  plugin ->
    println ("${plugin.getDisplayName()} (${plugin.getShortName()}): ${plugin.getVersion()}")
}
```

### [List plugin and dependencies](https://stackoverflow.com/a/56864983/2940319)
```groovy
println "Jenkins Instance : " + Jenkins.getInstance().getComputer('').getHostName() + " - " + Jenkins.getInstance().getRootUrl()
println "Installed Plugins: "
println "=================="
Jenkins.instance.pluginManager.plugins.sort(false) { a, b -> a.getShortName().toLowerCase() <=> b.getShortName().toLowerCase()}.each { plugin ->
   println "${plugin.getShortName()}:${plugin.getVersion()} | ${plugin.getDisplayName()} "
}

println ""
println "Plugins Dependency tree (...: dependencies; +++: dependants) :"
println "======================="
Jenkins.instance.pluginManager.plugins.sort(false) { a, b -> a.getShortName().toLowerCase() <=> b.getShortName().toLowerCase()}.each { plugin ->
   println "${plugin.getShortName()}:${plugin.getVersion()} | ${plugin.getDisplayName()} "
   println "+++ ${plugin.getDependants()}"
   println "... ${plugin.getDependencies()}"
   println ''
}
```