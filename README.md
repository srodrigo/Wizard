#Android Wizard
![Logo](art/logo.png)

##Importing to your project
Add dependency to your build.gradle file:

```java
NOT PUBLISHED YET
dependencies {
    compile 'me.panavtec.wizard:1.0.0'
```
##Basic usage

Create a WizardPage for each fragment you need to present, the only mandatory method to override is "createFragment()":

```java
public class WizardPage1 extends WizardPage<Fragment1> {
    @Override public Fragment1 createFragment() {
        return new Fragment1();
    }
}
```

Create a wizard in your activity in this way:

```java
@Override protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        new Wizard.Builder()
                .activity(this)
                .pageList(new WizardPage[]{
                        new WizardPage1(),
                })
                .build();
...
```

And call init:

```java
wizard.init();
```

Now the wizard is initalized, to use navigation just call, "**navigatePrevious**", "**navigateNext**" or "**returnToFirst**". 
You can get the current fragment by calling "getCurrentFragment"

##Advanced usage

###Animation transitions
You can declare animations for enter/exit fragments when creating Wizard in this way:

```java
new Wizard.Builder()
                .activity(this)
                .containerId(android.R.id.content)
                .enterAnimation(R.anim.card_slide_right_in)
                .exitAnimation(R.anim.card_slide_left_out)
                .popEnterAnimation(R.anim.card_slide_left_in)
                .popExitAnimation(R.anim.card_slide_right_out)
                .pageList(new WizardPage[]{
                        new WizardPage1(),
                        new WizardPage2(),
                        new WizardPage3()
                })
                .build();
```

This xml animations are uploaded to the sample project.


###Provide a custom container for fragment navigation
if you don't set the containerId attribute, Wizard uses android.R.id.content by default but you can personalize a custom container by calling:

```java
new Wizard.Builder()
                .activity(this)
                .containerId(R.id.my_container)
                .pageList(new WizardPage[]{
                        new WizardPage1(),
                })

```

###Customize ActionBar in navigation
In wizard page you can override method "setupActionBar" for customize your actionbar on each navigated fragment, ex:

```java
@Override public void setupActionBar(ActionBar supportActionBar) {
        super.setupActionBar(supportActionBar);
        supportActionBar.hide();
    }
```

In this example when the fragment is navigated, the actionbar will be hidden. 

###Option menu in Actionbar

If you have a Fragment that uses actionbar option menus, you can override "hasOptionMenu" and return true to invalidate the option menus when navigating.


###Dagger Tips
If you are using Dagger in your project I suggest to use Wizard in this way.
**ActivityModule**:

```java
@Provides @Singleton Wizard provideWizard(ActionBarActivity activity,
                                              SampleWizardPage page) {
        return new Wizard.Builder()
                .activity(activity)
                .containerId(android.R.id.content)
                .pageList(new WizardPage[]{
                        page,
                        ...
                })
                .build();
    }
    
    @Provides @Singleton SampleWizardPage provideSampleWizardPage(ActionBarActivity activity) {
        return new SampleWizardPage(activity);
    }

    
```

**Activity**:

```java
@Inject Wizard wizard;

@Override protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        wizard.init();
        }
```

**Fragment**:

```java
@Inject Wizard wizard;

@Override protected void someOnClickAction() {
        wizard.navigateNext();
        }
```
