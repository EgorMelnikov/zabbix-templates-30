# zabbix-templates-30

## Introduction
This repository contains my efforts to create a repository based on the templates that were shipped with Zabbix 3.0.
The shipped templates are far from usable for a production deployment. They are also kept fairly basic as to not scare new users away from the product.
However, Zabbix has some very powerful features that, if used correctly, will bring your monitoring to a new level.

Thus, my goals with this repo are as follows:

- Create a logical and hierarchial structure in template linking to ease template maintenance
- Make templates as flexible as currently possible by utilizing linking, LLD, (context aware) user macros and calculated items where possible
- Set up a naming convention to ease working with templates from automated API scripts
- Utilize the description fields for templates, items and triggers to document behaviour inline as much as possible.
- Graph ALL the things, visualizing gathered data is key in your daily operations
- When plausible, define host screens for easy access to relevant data.

## Template Hierarchy

The template hierarchy uses inherentence via template linking to model a monitoring profile which can be linked to a host.
The advantage of using template linking in multiple layers is that you can override usermacros on any of the levels to achieve a different behaviour for items or triggers that reside on a lower level in the chain.


### Modules

(Functional) Modules are the lowest layer of templates used in the template hierarchy. They contain items, triggers and graphs to only monitor a specific function a host. For instance, the template `t_mod_icmp_ping` only monitors host reachability via ICMP.

The usermacros used in the module templates provide sane defaults for item parameters and triggers to function. If needed, they can be overruled from higher level templates.

It is allowed to create triggers on these templates, however, as they only give a very narrow insight into only a narrow part of the overal host state, it is recommended to only configure triggers with a severity level up to the Average severity level.

Graphs can be created that use the items from the template. Host screens are generally not useful on this level.

### Services

Service templates are used to combine data from lower module templates into a view of a particular service on a host.
The service level templates should not contain any items, except for calculated items that calculate a service state based on item values from lower level items and metadata items.

Triggers on this level in the template hierarchy will represent the state of a certain service that the host provides and therefor can be up to 'High' in severity level.

Graphs can combine items from multiple module level templates if so desired.
Also, it makes sense to create host screens on this level to give a broader overview of a service state.

### Roles

Role templates merge the various services into a host role, for instance `LAMP server` or `Access Switch`.

If needed, calculated items and triggers can be used to provide host level information. Triggers can go up to the disaster severity level.

The role level templates are very well suited to configure a host level dashboard in the form of a host screen.

### Profiles

The sole purpose of the profile template is to add a layer in the hierarchy where we can override lower level usermacros to set default macros for a host role within a certain environment.
Profile templates don't contain any graphs, screens, items or triggers, all of those are inherited from lower level templates.

The profile template should be the only template that is be linked to an actual host.

## Naming conventions

### Templates

#### Visible name
Templates have 2 name fields in Zabbix, the 'Template name' and the 'Visible name'.
We use the 'Visible name' to represent the template in human readable form as it is the name a Zabbix operator will normally see when using the GUI.
The name always starts with the string 'Template' followed by a clear description of it's function.
The visible name can contains spaces to seperate words.

#### Template name
The more technical 'Template name' is the name that we will mostly use in scripts and within Zabbix configuration.
As such, we will define stricter rules in using this naming field.
Spaces will not be allowed, as this could break some poorly written scripts and makes parsing of the names more difficult.
An underscore '_' is used as a word seperator within the name. Template names will be written in lowercase.

Names consist of a number of fields and will always start with the first field containing the prefix 't_' to indicate the fact that we are dealing with a template. 
The second field contains a denominator for the template type (see [Hierarchy](#template-hierarchy)):

- Modules: mod
- Services: serv
- Roles: role
- Profiles: prof


### Host groups



### Usermacros
By Zabbix specifications, user macros are always written in upper case and cannot contain spaces (see [documentation](https://www.zabbix.com/documentation/3.0/manual/config/macros/usermacros)).
Therefor, we will use the underscore as a word seperator within the macro name.

The macro name consists of several fields. The first field specifies the functional field for the macro. This will allow us to quickly see where a macro is used.

- I_:	Items (parameters)
- T_:	Triggers (function parameters and expressions)


