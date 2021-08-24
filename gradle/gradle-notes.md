# Java project gradle build

## Create Project Using spring command line tool

```
spring init --boot-version 2.5.3 --java-version 1.8 --language java \
        --artifactId aproject --groupId nogroup \
        -d data-jpa,devtools,lombok,postgresql,validation,web \
        --type gradle-project -v 0.0.1

```

## Add eclipse plugin
```
sed -i "/id .java./a \\\tid 'eclipse'" build.gradle
```

## Gradle goals/tasks
```
./gradlew eclipse
./gradlew cleanEclipse
./gradlew compileJava
./gradlew compileTestJava
./gradlew bootRun
```

## Ease development (fake jar technique)
```
cat <<EOF >>build.gradle
tasks.register('printclasspath') {
    doLast {
        println "\${sourceSets.main.compileClasspath.asPath}"
        println "\${sourceSets.test.compileClasspath.asPath}"
    }
}
EOF


./gradlew printclasspath | grep 'jar$' | tr ':' '\n' | sort -u | tee .classpath.txt

jars="$(tr '\n', ':' < .classpath.txt):$PWD/build/classes/java/main:$PWD/build/classes/java/test"
echo "Class-Path# $jars" | sed 's/::/:/g' | sed 's/:/ /g' | sed 's/#/:/' \
        | sed -r 's/([[:alnum:]])#/\1:/g;s/(.{65})/&@/g' \
        | tr '@' '\n' | sed -r '2,$s/^/ /' > manifest
jar cmf manifest classpath.jar
rm manifest
```

## build.xml 'cause ant is faster
```
project=$(basename $PWD)
cat <<EOF > build.xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="$projet" default="compile">

  <property name="targetclasses" value="\${basedir}/build/classes/java/main"/>
  <property name="targettestclasses" value="\${basedir}/bin/test"/>
  <property name="targetgenerated" value="\${basedir}/build/classes/java/test"/>

  <path id="my.path">
    <pathelement path="\${basedir}/classpath.jar"/>
  </path>

  <target name="init">
    <mkdir dir="\${targetclasses}"/>
    <mkdir dir="\${targettestclasses}"/>
    <mkdir dir="\${targetgenerated}"/>
  </target>

  <target name="clean">
    <delete dir="\${basedir}/build"/>
    <delete dir="\${basedir}/bin"/>
  </target>

  <target name="compile" depends="init">
    <javac srcdir="src/main/java" destdir="\${targetclasses}" classpathref="my.path"
        includeantruntime="false" debug="true">
      <compilerarg line="-s \${targetgenerated}"/>
      <compilerarg line="-parameters"/>
      <compilerarg line="-nowarn"/>
      <compilerarg line="-target 8"/>
      <compilerarg line="-source 8"/>
      <compilerarg line="-encoding UTF-8"/>
    </javac>
    <javac destdir="\${targettestclasses}" classpathref="my.path"
      includeantruntime="false" debug="true">
      <src path="src/test/java"/>
      <compilerarg line="-s ${targetgenerated}"/>
      <compilerarg line="-parameters"/>
      <compilerarg line="-nowarn"/>
      <compilerarg line="-target 8"/>
      <compilerarg line="-source 8"/>
      <compilerarg line="-encoding UTF-8"/>
    </javac>
  </target>
</project>
EOF
```

## junit.sh to execute single test class
```
#!/usr/bin/env bash

if [[ "$#" -lt 1 ]]; then
  echo "no file name"
  return
fi

file=$(find src/test -name $1 -o -name $1.java)
classname=$(echo $file | sed -E 's:.*src/test/java/::;s:/:.:g;s/\.java//')
$JAVA_HOME/bin/java -cp classpath.jar:{{junit-platform-console-standalone-jar}} \
  org.junit.platform.console.ConsoleLauncher \
  --disable-banner \
  -c $classname
```
