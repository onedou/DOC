## 一、生成证书

keytool -v -genkey -alias tomcat -keyalg RSA -keystore D:\java\tomcat\tomcat.keystore  -validity 36500

![image](https://user-images.githubusercontent.com/3422640/32779876-f3e61b76-c904-11e7-9568-5b93c2b83454.png)

## 二、tomcat的server配置

1、注释掉8080端口配置

    <!--<Connector port="8080" protocol="HTTP/1.1"
                   connectionTimeout="20000"
                   redirectPort="8443" />-->

2、取消注释8443端口配置，并改为443端口（访问不加端口的设置），将生成的正式和密码配置到keystoreFile="D:\java\tomcat\tomcat.keystore" keystorePass="123456"

     <Connector port="443" protocol="org.apache.coyote.http11.Http11NioProtocol"
                 maxThreads="150" SSLEnabled="true" 
                 keystoreFile="D:\java\tomcat\tomcat.keystore" keystorePass="123456"
                 >
          <!--<SSLHostConfig>
              <Certificate certificateKeystoreFile="conf/localhost-rsa.jks"
                           type="RSA" />
          </SSLHostConfig>-->
      </Connector>

3、更改8009端口未443

<Connector port="8009" protocol="AJP/1.3" redirectPort="443" />

## 三、访问，输入https://localhost/

![image](https://user-images.githubusercontent.com/3422640/32779963-3c631796-c905-11e7-8990-1fcd448dac54.png)
