# Load your OS X Bash Environment in Eclipse

*Published on July 18, 2014*

Launch Eclipse using Terminal: `/bin/bash -ic /Applications/eclipse/Eclipse.app/Contents/MacOS/eclipse` (your path may be different)

Add `<property environment="env" />` to your `build.xml` and use the OS X environment variables that were set when Eclipse was launched however you want!

For instance, `${env.GEM_HOME}` will give you the directory to the gemset that was loaded when Eclipse was launched, which might be useful if you're using RVM to manage Ruby binaries and per-project gemsets:

```
<apply executable="${env.GEM_HOME}/sass" dest="../css" verbose="true" force="true" failonerror="true">
  ...
</apply>
```