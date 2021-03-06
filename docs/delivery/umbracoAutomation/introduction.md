# Umbraco Automation

We have created a commercial package called Yuzu Delivery Import that handles all aspects of Umbraco Automation in Yuzu. It is split into the following sections

- Viewmodel mapping : an overview of all the generated viewmodels and their inter relationships mapped to document type definitions.
- Schema creation : creating document types and data types from the generated viewmodel definitions.
- Content creation : using definition page states and images from the definition pattern library to create content in Umbraco.
- Bespoke mapping : configuring mappings to route data into grouped or global document types to prevent duplication and reduce complexity of CMS schema.
- Manual mapping : replace default mapping on any viewmodel type or property to handle complex mappings and use external data.

There are two versions of this package, lite and pro. The lite package only allows global mapping for a number of times using the Map All Viewmodels button in the footer. The pro version is licensed on a subscription per developer basis as part of the Yuzu Developer package. More information can be found at [Balanced.dev](http://balanced.dev)

The dashboard for Yuzu Delivery Import is found in the **Settings** section of Umbraco under the **User Interface Integration** group.

## Configuration

### Startup

| Property    			    	 | Purpose 			                 			             |
| -------------------------------| ----------------------------------------------------------|
| IsActive		                 | Turns off Yuzu Import*                                    |
| ViewModelQualifiedTypeName	 |                                                           |
| UmbracoModelsQualifiedTypeName |                                                           |
| DataTypeFolder		         | Umbraco data type folder that new data types are added to |
| DataLocations                  | Location of json data states for content import           |
| ImageLocations                 | Location of images for content import                     |
| CustomConfigFileLocation       | Location of the custom mapping config file                | 

* IsActive is always false when project compiled in Release mode

### Ignores

Usually there are elements you want to be ignored by the import tool. These config properties allow you to define what is ignored globally or per viewmodel. 

Each config property is a collection of string or dictionary of string, we have created an extension method so we can use generics of strongly types to add ignore.

``` c#
config.IgnoreViewmodels.Add<vmPage_HomePage>();
```

is the same as

``` c#
config.IgnoreViewmodels.Add("vmPage_HomePage");
```

| Property    			    	 | Purpose 			                 			             |
| -------------------------------| ----------------------------------------------------------|
| IgnoreViewmodels               | Ignore from viewmodel mapping and Umbraco generation      |
| IgnorePropertiesInViewModels   | Ignore a named property in a viewmodel                    |
| IgnoreProperties            	 | Ignore a named property in all viewmodels                 |

The property names `_ref` and `_modifiers` are always added to blocks and are ignored in import. 
The property name `AdditionalProperty` is created during viewmodel generation and is ignore in import.

By default any data structures from definition should be defined in an external library. For example, the viewmodel from vmBlock_DataImage is defined in the YuzuDelivery.Umbraco.Blocks package. These definitions are set as ignored during the startup of each specific definition package. i.e The forms package adds the form builder and form elements to the IgnoreViewModels.

### Umbraco

| Property    			    	 | Purpose 			                 			             | Default |
| -------------------------------| ----------------------------------------------------------|---------|
| DataTypeFolder		         | Umbraco data type folder that new data types are added to |         |
| DefaultPropertyGroup           | The default property group properties are added to        | Content |
| GridRowConfigs                 | Grid row setup                                            |         |

Grid configs are defaulted to 

``` c#

GridRowConfigs = new List<GridRowConfig>();
GridRowConfigs.Add(new GridRowConfig(true, "100", "12", "12"));
GridRowConfigs.Add(new GridRowConfig(false, "50/50", "6", "6,6"));
GridRowConfigs.Add(new GridRowConfig(false, "33/66", "4,8", "4,8"));
GridRowConfigs.Add(new GridRowConfig(false, "66/33", "4,8", "8,4"));
GridRowConfigs.Add(new GridRowConfig(false, "33/33/33", "4", "4,4,4"));

```

where 100 is the default row config.

## Logging

Most of the processes in Yuzu Import log errors, warnings and info to the Umbraco error log in Serilog. We have added a dictionary of all the logging string to filter by specific errors on messages.
