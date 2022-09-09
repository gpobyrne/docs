<!-- toc -->

# Create a new Plugin

With the introduction of the gradle build system (Freeplane >= 1.4.1), the
"create-plugin" ant task is deprecated because adding plugins "manually"
has become very simple using gradle.

## Steps
Here are the steps you need to perform:

* choose a short name for your plugin (e.g. <tt>freeplane_plugin_foo</tt>, the name <tt>freeplane_plugin_*</tt> is mandatory)
* include the plugin in <tt>settings.gradle</tt>
* create <tt>freeplane_plugin_foo/build.gradle</tt> by copying the <tt>build.gradle</tt> from any other plugin (<tt>freeplane_plugin_latex</tt> <tt>freeplane_plugin_openmaps</tt> are quite simple)
* add third-party dependencies (dependencies section) and OSGi imports (packages you need from core/other plugins) to your build.gradle
    * if your third-party dependencies are not available in maven.org, uses files() and commit them to version control (<tt>freeplane_plugin_foo/lib</tt>)
* create <tt>freeplane_plugin_foo/src/main/java/org/freeplane/plugin/foo/Activator.java</tt> by copying from another plugin and then removing things you don't need
* TODO: need to adapt eclipse launcher?
* test your plugin by building and running freeplane
* when committing, make sure not to commit .project / .classpath files as they are generated by gradle

## Add options to the OptionPanel

The core plugins register their options via the configuration file
<tt>freeplane/resources/xml/preferences.xml</tt>. Plugins can do it quite
similar by defining their own <tt>preferences.xml</tt> (by convention in
<tt>freeplane_plugin_<plugin>/org/freeplane/plugin/<plugin>/preferences.xml</tt>);
here from the *script* plugin (the latex plugin is also a good example):
```xml
<?xml version="1.0" encoding="UTF-8"?>
<preferences_structure>
   <tabbed_pane>
      <tab name="plugins">
         <separator name="scripting">
            <boolean name="execute_scripts_without_asking" />
            <boolean name="execute_scripts_without_file_restriction" />
            <boolean name="execute_scripts_without_network_restriction" />
            <boolean name="execute_scripts_without_exec_restriction" />
            <boolean name="signed_script_are_trusted" />
            <string name="script_user_key_name_for_signing" />
            <string name="script_directories" />
         </separator>
      </tab>
   </tabbed_pane>
</preferences_structure>
```

The option names have to be added to <tt>Resource_en.properties</tt> (excerpt):
```
OptionPanel.execute_scripts_without_asking = Scripts should be carried out without confirmation?
OptionPanel.execute_scripts_without_asking.tooltip = <html>Freeplane scripts are principally able...
OptionPanel.execute_scripts_without_exec_restriction = Permit to Execute other Applications (NOT recommended)
```

For an example of how options can be registered at the option panel
see <tt>org.freeplane.plugin.script.ScriptingRegistration</tt>:
```groovy
private void addPropertiesToOptionPanel() {
    final URL preferences = this.getClass().getResource("preferences.xml");
    if (preferences == null)
        throw new RuntimeException("cannot open preferences");
    modeController.getOptionPanelBuilder().load(preferences);
}
```

The options can be queried like this:
```groovy
String executeWithoutAsking = ResourceController.getResourceController()
    .getProperty("execute_scripts_without_asking");
```

## Register Actions
Please see OpenMapsRegistration.java in the <tt>freeplane_plugin_openmaps</tt> plugin.

## Register Icon Mouse click listener
Please see OpenMapsRegistration.java in the <tt>freeplane_plugin_openmaps</tt> plugin.

<!-- ({Category:Coding}) -->
