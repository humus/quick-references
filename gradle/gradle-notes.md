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
./gradlew eclipseClean
./gradlew compileJava
./gradlew compileTestJava
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
