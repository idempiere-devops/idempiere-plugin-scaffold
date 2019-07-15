# iDempiere Plugin Generator

This project creates an iDempiere plugin skeleton.

- Plugin example [com.ingeint.template](com.ingeint.template).

- Target platform example https://bitbucket.org/ingeint/ingeint-idempiere-target-platform/

## Getting Start

Create a new plugin with the command `make`.

## Standard New Plugin

- New callout
    * Name: C{callout name}
    * Package: {package root}.callout
    * Example: com.ingeint.callout.CStringFormat
    
- New process
    * Name: P{process name}
    * Package: {package root}.process
    * Example: com.ingeint.process.PGenerateWithholding
    
- New form
    * Name: F{form name}
    * Package: {package root}.form
    * Example: com.ingeint.form.FMultiPayment
    
- New event
    * Name: E{event name}
    * Package: {package root}.event
    * Example: com.ingeint.event.EAfterCompleteInvoice
    
- New model (extends class X)
    * Name: M{table name without prefix}. 
    * Package: {package root}.model
    * Example: com.ingeint.model.X_IGI_TableExample -> com.ingeint.model.MTableExample
    
## Folder estructure

```
    Plugin Template
        |_.settings
        |   |_org.eclipse.core.resources.prefs
        |   |_org.eclipse.jdt.core.prefs
        |   |_org.eclipse.m2e.core.prefs
        |   |_org.eclipse.pde.core.prefs
        |_.classpath
        |_.project
        |_.hgignore
        |_.gitignore
        |_build.properties
        |_LICENSE
        |_README.md
        |_pom.xml
        |_META-INF
        |   |_MANIFEST.MF
        |   |_2Pack_X.X.X.zip
        |_OSGI-INF
        |   |_CalloutFactory.xml
        |   |_EventManager.xml
        |   |_FormFactory.xml
        |   |_ModelFactory.xml
        |   |_ProcessFactory.xml
        |_src
            |_{root package}
                |_base (plugin core)
                |   |_bundle
                |   |   |_BundleInfo.java (gets plugin information dynamically)
                |   |_util
                |   |   |_TimestampUtil.java
                |   |_callout
                |   |   |_CustomCallout.java (IColumnCallout implementation)
                |   |   |_CustomCalloutFactory.java (IColumnCalloutFactory implementation)
                |   |_event
                |   |   |_CustomEventFactory.java (AbstractEventHandler implementation)
                |   |   |_CustomEvent.java (for event implementation)
                |   |_form
                |   |   |_CustomFormFactory.java (IFormFactory implementation)
                |   |   |_CustomForm.java (IFormController implementation)
                |   |_model
                |   |   |_CustomModelFactory.java (IModelFactory implementation)
                |   |_process
                |   |   |_CustomProcessFactory.java (IProcessFactory implementation)
                |   |   |_CustomProcess.java (SvrProcess implementation)
                |   |_service
                |       |_component (pugin components)
                |           |_CalloutFactory.java (register class callout)
                |           |_EventManager.java (register class event handler)
                |           |_FormFactory.java (register class form)
                |           |_ProcessFactory.java (register class process)
                |           |_ModelFactory.java (register class model)
                |_callout (new callouts, extends CustomCallout)
                |_event (new events, extends CustomEventHandler)
                |_form (new forms, extends CustomFormController)
                |_process (new processes, extends CustomProcess)
                |_model (autogenerated models)
        
```
 
## How it works

- New callout
    * Create callout in package `callout`, extends from `CustomCallout`
    * Register callout in `base.service.component.CalloutFactory`. Example:

```java
    protected void initialize() {
        registerCallout(MTableExample.Table_Name, MTableExample.COLUMNNAME_Text, CPrintPluginInfo.class);
    }
```

- New process
    * Create process in package `process`, extends from `CustomProcess`
    * Register process in `base.service.component.ProcessFactory`. Example:

```java
    protected void initialize() {
        registerProcess(PPrintPluginInfo.class);
    }
```

- New form
    * Create form in package `form`, extends from `CustomForm`
    * Register form in `base.service.component.FormFactory`. Example:

```java
    protected void initialize() {
        registerForm(FPrintPluginInfo.class);
    }
```

- New event
    * Create event in package `event`, extends from `CustomEvent`
    * Register event in `base.service.component.EventManager`. Example:

```java
    protected void initialize() {
        registerEvent(IEventTopics.DOC_BEFORE_COMPLETE, MTableExample.Table_Name, EPrintPluginInfo.class);
    }
```

- New model (extends form class X)
    * Create model in package `model`, extends class `X`. Example: `X_TL_TableExample -> MTableExample`
    * Register model in `base.service.component.ModelFactory`. Example:

```java
    protected void initialize() {
        registerModel(MTableExample.Table_Name, MTableExample.class);
    }
```

## Adding a new library

Add the new dependency (`artifacItem`) to the [pom.xml](com.ingeint.template/pom.xml) file in the `artifactItems` attribute, example:

```xml
    <artifactItems>
        <artifactItem>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>28.0-jre</version>
        </artifactItem>
    </artifactItems>
```

Then, add a new classpath entry in the [.classpath](com.ingeint.template/.classpath) file, example:
```xml
    <classpathentry kind="lib" path="lib/guava.jar"/>
```

Verify you are including the folder `lib` in the [build.properties](com.ingeint.template/build.properties) file, exaple:

```properties
bin.includes = .,\
               META-INF/,\
               OSGI-INF/,\
               lib/

```

Finally, add the new dependency in de [MANIFEST.MF](com.ingeint.template/META-INF/MANIFEST.MF) file as a `Bundle-ClassPath` attribute, example:

```manifest
Bundle-ClassPath: .,
 lib/guava.jar
 ```
 
 ## Target Platform
 
 A target platform allows you to download dependencies and build the jar plugin, there are some examples:
 
 - https://bitbucket.org/ingeint/ingeint-idempiere-target-platform/src/master/
 - https://bitbucket.org/CarlosRuiz_globalqss/globalqss-idempiere-lco
 - https://wiki.idempiere.org/en/Building_iDempiere_Plugins_with_Maven
 
 After the plugin creation it's necessary to create or update the target platform `pom.xml` file
 and then, run (inside the target platform path) `mvn verify` ([see](https://bitbucket.org/ingeint/ingeint-idempiere-target-platform/src/185b9dc3652a9df1c3457c2c67b7e5a637f7cac7/Makefile#lines-2)).
 

