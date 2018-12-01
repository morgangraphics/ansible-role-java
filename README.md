# Ansible Role: Java

Installs JAVA on Debian/Ubuntu and RHEL/CentOS


The purpose of this Role is to leave the default OS as intact as possible. As a result there are things that are installed used and then uninstalled or not installed at all to preserve that methodology.

I try to preserve backwards compatibility wherever possible as long as it makes sense. For example, RedHat's new Package manager DNF could be switched to YUM to install OpenJDK 1.7. However, that violates the purpose of the methodology to leave the default OS intact. As a result I drop support for OpenJDK 1.7 on newer versions of RedHat because that is the default action RedHat takes. However, older versions of RedHat and/or AWS Flavors of Linux are covered as long as YUM is the default Package Manager.


## Unsupported Java versions
Support for Oracle Java 6 and 7 were removed from public download in June of 2017.
This means you need a Oracle support contract to get access to them.

From Oracle [downloads page](http://www.oracle.com/technetwork/java/javase/downloads/index-jsp-138363.html)

 > "Updates for Java SE 7 released after April 2015, and updates for Java SE 6 released after April 2013 are only available to Oracle Customers through My Oracle Support (requires support login).

 > Java SE Advanced offers users commercial features, access to critical bug fixes, security fixes, and general maintenance".

This Ansible role **does not provide support** for Oracle Java 6/7 or Oracle JRE

The Oracle JRE versions of Java change frequently and require manual download and installation. While this role could be scripted to handle such tasks, there is no need for me to do so at this time. Others have taken it upon themselves to handle the [insanity involved in doing so](https://gist.github.com/P7h/9741922)

## Supported Java versions

### Ubuntu
|             | JDK      | JRE      |
|-------------|----------|----------|
| **Oracle**  | 8,9      |          |
| **OpenJDK** | 6,7,8,9+ | 6,7,8,9+ |


### RHEL DNF (Fedora 21 +)
|             | JDK     | JRE     |
|-------------|---------|---------|
| **Oracle**  | 8,9     |         |
| **OpenJDK** | 8,9+    | 8,9+    |


### RHEL/CentOS YUM
|             | JDK      | JRE      |
|-------------|----------|----------|
| **Oracle**  | 8,9      |          |
| **OpenJDK** | 6,7,8,9+ | 6,7,8,9+ |


```
- role: ansible-role-java
  install_type: jdk
  java_version: openjdk-7
```

The above **WILL NOT** work for RedHat (Fedora > 21). However, it is still perfectly valid for CentOS and older versions of RedHat (Fedora < 21)


## Example Playbooks

Naming convention is {oracle|openjdk}-{version}

e.g. `oracle-8` or `openjdk-9`


#### Simple
``` yaml
- hosts: all
  roles:
    # Installs openjdk-8-jre by default
    - role: ansible-role-java
```

``` yaml
- hosts: all
  roles:
    # Installs openjdk-8-jdk
    - role: ansible-role-java
      install_type: "jdk"
      java_home: False

```
#### More Complex
``` yaml
- hosts: dev1
  roles:
    - role: ansible-role-java
      java_version: "oracle-8"

- hosts: dev2
  roles:
    # Install OpenJDK 7,8 & 9 JRE setting 8 as the Default
    - role: ansible-role-java
      install_type: "jdk"
      java_version: "openjdk-7"

    - role: ansible-role-java
      install_type: "jdk"
      java_version: "openjdk-8"
      default: True

    - role: ansible-role-java
      java_version: "openjdk-9"

```


## Notes

This role runs as `root` user

This role takes advantage of APT/DF/YUM default JAVA repository version for the version of the OS you are using; meaning just including the role will install **openjdk-8-jre** by default. **This should suffice for most applications** unless, for example, the default JAVA repository version is `openjdk-9-jre` for your OS.

Any Oracle versions of Java are **JDK** by default (JDK also includes JRE)

If installing multiple versions of Java, last one installed is set as default unless specified

`openjdk-6` is not supported on Ubuntu 16.04 Xenial or RHEL Fedora 21+
`openjdk-7` is not supported on RHEL Fedora 21+



## Role Variables

Available variables are listed below, along with default values see [defaults/main.yml]( defaults/main.yml)

Run System Update before Install
*It is recommended that this be set to True, however, if you do this as part of another step this can be False*

    system_update: True

The Java type version to install. Options are `jre` or `jdk`

    install_type: "jre"

Java version to install

    java_version: "default"


Defaulted Version of Java

    default: False

Set JAVA_HOME variable

    default: True

Add JAVA_HOME to PATH

    default: True


## TODO:
1. RedHat Oracle Install
https://github.com/ansiblebit/oracle-java/blob/master/tasks/redhat/main.yml

## Dependencies

None.



## License

MIT / BSD

## Author Information

dm00000 via MORGANGRAPHICS, INC
