
# Byteman <!-- omit in toc -->

## Description

Byteman is an agent for the JVM that add trace and help for debug the code at runtime.

## Installation

### For Jboss Server

Extract binaries from <http://downloads.jboss.org/byteman/3.0.10/byteman-download-3.0.10-bin.zip> on your server in directory : ```<BYTEMAN_HOME>```

```sh
# Define the environnement variable BYTEMAN_HOME
export BYTEMAN_HOME=/home/mdm-unix/tools/byteman-download-4.0.2/

# Configure Jboss
vi <jboss_home>/bin/standalone.conf

# Add the following configuration for JVM arguments
JAVA_OPTS="$JAVA_OPTS -javaagent:${BYTEMAN_HOME}/lib/byteman.jar=script:${BYTEMAN_HOME}/rules/byteman.btm,boot:${BYTEMAN_HOME}/lib/byteman.jar,listener:true -Dorg.jboss.byteman.transform.all"

#Restart Jboss
cd $HOME;nohup $JBOSS_HOME/bin/standalone.sh -c standalone-full.xml -b 0.0.0.0 >> $HOME/byteman.log &

# Create the rule file
vi ${BYTEMAN_HOME}/rules/byteman.btm

RULE SamlEndpoint.handle Call
CLASS SamlEndpoint
METHOD handle
AT ENTRY
IF true
DO traceln("Appel SamlEndpoint.handle " + $0)
ENDRULE

# Inject the rule file
${BYTEMAN_HOME}/bin/bmsubmit.sh -l ${BYTEMAN_HOME}/rules/byteman.btm```
```

## Rules files

**Trace methode call with first parameter :**

```text
RULE SamlEndpoint.handle Call
CLASS SamlEndpoint
METHOD handle
AT ENTRY
IF true
DO traceln("Appel SamlEndpoint.handle " + $0)
ENDRULE
```

**Trace on return method with all parameter :**

```text
RULE SamlEndpoint.handle Return
CLASS SamlEndpoint
METHOD handle
AT EXIT
IF true
DO traceln("SSG Out SamlEndpoint.handle " + $!)
ENDRULE
```
