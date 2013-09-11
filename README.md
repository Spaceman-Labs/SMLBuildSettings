SMLBuildSettings
================


#### Using the merged xcconfig file.
![Screenshot](http://spacemanlabs.com/github/SMLBuildSettings/config-files.png)

![Screenshot](http://spacemanlabs.com/github/SMLBuildSettings/config-setting.png)

#### Settings up scheme scripts

Open the schemes editor. (cmd + <)
Add a new `run script` action to the Pre-actions under Build.

![Screenshot](http://spacemanlabs.com/github/SMLBuildSettings/scheme-action.png)

#### Merge partial xcconfig files

	ruby "${SRCROOT}/SMLBuildSettings/configurations.rb" --reset "${SRCROOT}/SMLBuildSettings"
	ruby "${SRCROOT}/SMLBuildSettings/configurations.rb" --merge Config-1.xcconfig Config-2.xcconfig "${SRCROOT}/Configurations"
	
#### Example Output

Config-1.xcconfig
	
	GCC_PREPROCESSOR_DEFINITIONS = CONFIG_FLAG_A=1 $(inherited)

Config-2.xcconfig
	
	GCC_PREPROCESSOR_DEFINITIONS = CONFIG_FLAG_B=1 $(inherited)
	
Config-Merged.xcconfig (after merge)

	//
	// **** Added By Configurations.rb ****
	// **** Path: Config-1.xcconfig ****
	//
	
	Config1xcconfigGCC_PREPROCESSOR_DEFINITIONS = CONFIG_FLAG_A=1 $(inherited)
	//
	// **** Added By Configurations.rb ****
	// **** Path: Config-2.xcconfig ****
	//
	
	Config2xcconfigGCC_PREPROCESSOR_DEFINITIONS = CONFIG_FLAG_B=1 $(inherited)
	//
	// **** Added By Configurations.rb ****
	//
	
	GCC_PREPROCESSOR_DEFINITIONS = $(Config1xcconfigGCC_PREPROCESSOR_DEFINITIONS) $(Config2xcconfigGCC_PREPROCESSOR_DEFINITIONS)
	GCC_PREPROCESSOR_DEFINITIONS = $(Config1xcconfigGCC_PREPROCESSOR_DEFINITIONS) $(Config2xcconfigGCC_PREPROCESSOR_DEFINITIONS)

Results

![Screenshot](http://spacemanlabs.com/github/SMLBuildSettings/merged.png)

#### Tips & Tricks
* Create multiple schemes to setup unique build configurations composed of xcconfig partials.
* Use environment variables and shell scripts to take conditional builds a step further.
* Always include `$(inherited)` in your partials, or some settings may be ignored
* Use `OTHER_LDFLAGS` to conditionally link libraries  

		OTHER_LDFLAGS = $(inherited) -l PonyDebugger
		GCC_PREPROCESSOR_DEFINITIONS = $(inherited) ENABLE_PONY_DEBUGGER=1


#### Complaints
Config-Merged.xconfig should be committed to your repo to keep Xcode's build configuration/xcconfig setup from complaining, this often means you'll have to be mindful to _not_ commit the merged version of it. This can be mitigated by using the reset flag of configurations.rb in a post build action.