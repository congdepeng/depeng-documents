
#Intellij IDEA. Thank you! You are the reason why I still can write Java.


## config files

Under the .IntelliJIdeaXX directory the following subdirectories can be found:
 - Config: /config
 - Plugins: /config/plugins
 - Caches: /system
 - Logs: /system/log (also contains automatic thread dumps)

```java
sudo rm -rf ~/.IntelliJIdea*
```

On Mac OS X IDEA uses the following directories:
 - Config: ~/Library/Preferences/IntelliJIdeaXX
 - System: ~/Library/Caches/IntelliJIdeaXX
 - Plugins: ~/Library/Application Support/IntelliJIdeaXX
 - Logs: ~/Library/Logs/IntelliJIdeaXX (starting from IntelliJ IDEA 9.0, older versions keep logs under System location)

```java
    sudo rm -rf ~/Library/Preferences/IntelliJIdea*; sudo rm -rf ~/Library/Caches/IntelliJIdea*
```