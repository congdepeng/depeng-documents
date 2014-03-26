
## Display
    - display all variables use 'set'
    - display a use 'echo "$JAVA_HOME"'

## Set
    - temporary available export PATH=${PATH}:/home/gradle/bin
    - permernant
    - permanent variable : save the temporary into shell profile file
        * zsh :

## Example: set Gradle
    1. export GRADLE_HOME=/home/depeng/apps/gradle-1.11
    2. export PATH=${PATH}:${GRADLE_HOME}