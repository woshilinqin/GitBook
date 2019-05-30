###### maven 配置分离

一般打包 jar 不是默认打包，默认打包会把所有的 lib 和配置打进去，但是一般只打源码即可。下面这个配置可以分离打包。

```xml
<build>
        <!-- 指定打包的名字 -->
        <finalName>spring-boot-application-config-location</finalName>
        <resources>
            <!--把配置文件放在jar包外面的config文件夹，方便修改-->
            <resource>
                <directory>src/main/resources</directory>
                <!-- 当前项目下target目录新建一个项目名文件夹里面config文件夹 ${project.artifactId} -->
                <targetPath>${project.basedir}/target/${project.artifactId}/config</targetPath>
                <includes>
                    <include>application.yml</include>
                </includes>
            </resource>
        </resources>

        <extensions>
            <extension>
                <groupId>org.apache.maven.wagon</groupId>
                <artifactId>wagon-ssh</artifactId>
            </extension>
        </extensions>
        <plugins>
            <!--打包操作-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <!--<version>${maven.jar.plugin.version}</version>-->
                <configuration>
                    <archive>
                        <manifest>
                            <!--表示需要加入到类构建路径-->
                            <addClasspath>true</addClasspath>
                            <!--classpathPrefix指定生成的Manifest文件中Class-Path依赖lib前面都加上路径,构建出lib/xx.jar-->
                            <classpathPrefix>lib/</classpathPrefix>
                            <mainClass>com.SpringBootAplicationConfigApplication</mainClass>
                        </manifest>
                    </archive>
                    <!--打包排除-->
                    <excludes>
                        <exclude>application.yml</exclude>
                    </excludes>
                    <outputDirectory>
                        ${project.build.directory}/${project.artifactId}
                    </outputDirectory>

                </configuration>
            </plugin>

            <!--拷贝依赖到jar外面的lib目录-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <!--<version>${maven.dependency.plugin.version}</version>-->
                <executions>
                    <execution>
                        <id>copy-lib</id>
                        <phase>prepare-package</phase>
                        <goals>
                            <goal>copy-dependencies</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>${project.build.directory}/${project.artifactId}/lib</outputDirectory>
                            <overWriteReleases>false</overWriteReleases>
                            <overWriteSnapshots>false</overWriteSnapshots>
                            <overWriteIfNewer>true</overWriteIfNewer>
                            <includeScope>compile</includeScope>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
		</build>
```

###### 插件上传部署

设置 install 命令为触发事件。

```xml
        <build>
        	<!-- 上传jar到linux -->
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>wagon-maven-plugin</artifactId>
                <version>1.0</version>
                <executions>
                    <execution>
                        <!--对应maven配置文件serverid-->
                        <!--<id>yun</id>-->
                        <phase>install</phase>
                        <goals>
                            <goal>upload</goal>
                            <goal>sshexec</goal>
                        </goals>
                        <configuration>
                            <fromDir>target/spring-boot-aplication-config</fromDir>
                            <url>scp://root:1qaz2wsx@192.168.2.149/root</url>
                            <excludes>lib/*</excludes>
                            <commands>
                                <!-- 杀死原来的进程 -->
                                <command>pkill -f pring-boot-application-config-location.jar</command>
                                <!--<command>chmod a+x restart.sh</command>-->
                                <command>nohup java -jar /root/spring-boot-application-config-location.jar</command>
                            </commands>
                            <!-- 显示运行命令的输出结果 -->
                            <displayCommandOutputs>true</displayCommandOutputs>
                        </configuration>
                    </execution>
                </executions>
            </plugin>

        </plugins>
    </build>
```

