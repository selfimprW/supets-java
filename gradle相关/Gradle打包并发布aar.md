Gradled打包和发布AAR打包
========================

参考网址：

1. [Gradle打包和发布](www.jianshu.comp8f957421fc78)
2. [gradle-bintray-plugin](https://github.com/bintray/gradle-bintray-plugin)
3. [android-maven-gradle-plugin](https://github.com/dcendents/android-maven-gradle-plugin)

# 1 引用插件

	classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.7'
    classpath 'com.github.dcendents:android-maven-gradle-plugin:1.5'
	

# 2 使用插件


	apply plugin: 'com.github.dcendents.android-maven'
	apply plugin: 'com.jfrog.bintray'
 -
   
	version = "1.0.1"   // #修改# // 这里是aar的版本号

	def siteUrl = 'https://github.com/saiwu-bigkoo/Android-PickerView'                        // #修改# // 项目的主页地址，我这里是我的PickerView项目在github的链接地址
	def gitUrl = 'https://github.com/saiwu-bigkoo/Android-PickerView.git'                     // #修改# // 项目 git 地址，我这里同样是用Github上的git地址
	group = "com.bigkoo"             // #修改# // 组名称，这个相当于依赖的时候 compile 'com.bigkoo:pickerview:1.0.1' “:”号前面的前缀
	
	install {
	    repositories.mavenInstaller {
	        // This generates POM.xml with proper parameters
	        pom {
	            project {
	                packaging 'aar'
	                name 'PickerView For Android'                                   // #修改# // 标题
	                url siteUrl
	                // Set your license
	                licenses {
	                    license {
	                        name 'The Apache Software License, Version 2.0'
	                        url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
	                    }
	                }
	                developers {
	                    developer {
	                        id 'sai'                                           // #修改# // 你的userid，昵称
	                        name 'sai.wu'                                       // #修改# // 用户名
	                        email 'sai.wu@bigkoo.com'                               // #修改# // 邮箱
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
	    from android.sourceSets.main.java.srcDirs
	    classifier = 'sources'
	}
	
	task javadoc(type: Javadoc) {
	    source = android.sourceSets.main.java.srcDirs
	    classpath += project.files(android.getBootClasspath().join(File.pathSeparator))
	}
	
	task javadocJar(type: Jar, dependsOn: javadoc) {
	    classifier = 'javadoc'
	    from javadoc.destinationDir
	}
	
	artifacts {
	    archives javadocJar
	    archives sourcesJar
	}
	
	Properties properties = new Properties()
	properties.load(project.rootProject.file('local.properties').newDataInputStream())
	bintray {
	    user = properties.getProperty("bintray.user")
	    key = properties.getProperty("bintray.apikey")
	    // 上面两个 user和key 需要留意一下，在local.properites 里面配置的
	    configurations = ['archives']
	    pkg {
	        repo = "maven"
	        name = "PickerView"                                                 // #修改# //  在 jcenter 上面的项目名字
	        websiteUrl = siteUrl
	        vcsUrl = gitUrl
	        licenses = ["Apache-2.0"]
	        publish = true
	    }
	}