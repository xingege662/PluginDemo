# PluginDemo
1. 
```
buildscript {
    repositories {
        jcenter()
        mavenCentral()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.2'
        classpath 'com.github.dcendents:android-maven-gradle-plugin:1.5'
        classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.7.2'
    }
}
```
2. 新建bintray.gradle的文件，进行配置

```
apply plugin: 'com.jfrog.bintray'
apply plugin: 'maven-publish'

def projectName = "plugin-test"
def mavenDesc = 'a plugin of test'
def baseUrl = 'https://github.com/xingege662'
def siteUrl = baseUrl
def gitUrl = "${baseUrl}/hotFix"
def issueUrl = "${baseUrl}/hotFix/issues"

def licenseIds = ['Apache-2.0']
def licenseNames = ['The Apache Software License, Version 2.0']
def licenseUrls = ['http://www.apache.org/licenses/LICENSE-2.0.txt']
def inception = '2017'

version = "1.0.0"
def username = 'xingege662'
install {
    repositories {
        mavenInstaller {
            pom.project {
                packaging 'aar'
                // Description
                name 'plugin-test'
                description mavenDesc
                url siteUrl

                // Archive
                groupId project.group
                artifactId archivesBaseName
                version project.version

                // License
                inceptionYear inception
                licenses {
                    licenseNames.eachWithIndex { ln, li ->
                        license {
                            name ln
                            url licenseUrls[li]
                        }
                    }
                }
                developers {
                    developer {
                        name username
                    }
                }
                scm {
                    connection gitUrl
                    developerConnection gitUrl
                    url siteUrl
                }
            }
        }
    }
}

task sourcesJar(type: Jar) {
    from sourceSets.main.allGroovy
    classifier = 'sources'
}


task javadocJar(type: Jar, dependsOn: groovydoc) {
    from groovydoc.destinationDir
    classifier = 'javadoc'
}

artifacts {
    archives javadocJar
    archives sourcesJar
}


bintray {
    user = BINTRAY_USER
    key = BINTRAY_KEY
    configurations = ['archives']
    pkg {
        repo = 'maven'
        name = projectName
        desc = mavenDesc
        websiteUrl = siteUrl
        issueTrackerUrl = issueUrl
        vcsUrl = gitUrl
        labels = ['gradle', 'plugin', 'time']
        licenses = licenseIds
        publish = true
        publicDownloadNumbers = true
    }
}
```

3. 项目的gradle文件进行引用

```
apply plugin: 'groovy'
apply plugin: 'maven'

group='com.cx.plugin'
version='1.0.0'


dependencies {
    compile gradleApi()
    compile localGroovy()
}

repositories {
    mavenCentral()
}

apply from: '../bintray.gradle'
```
4.执行命令

```
gradlew install 
gradlew bintrayUpload
```
