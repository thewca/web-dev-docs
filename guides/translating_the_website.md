---
title: Translating the Website
layout: default
parent: Guides
---

# TODO: add a note about how the website changes constantly, and translating the website is not a one time task, it's an ongoing commitment.

## Table of Contents

* [Existing translations](#existing-translations)
* [WIP translations](#wip-translations)
* [General notes](#general-notes)
* [Dealing with special keys](#dealing-with-special-keys)
   * [Using variables](#using-variables)
   * [Using html](#using-html)
   * [Dealing with pluralization](#dealing-with-pluralization)
* [The "Internationalize" application](#internationalize)
   * [Getting started](#getting-started)
   * [Synchronizing/Updating a remote locale](#synchronizingupdating-a-remote-locale)
   * [A note about pluralization](#a-note-about-pluralization)
* [Bonus: Translating non WCA-specific parts of the application](#bonus-translating-non-wca-specific-parts-of-the-application)
* [Bonus: Dev notes](#bonus-dev-notes)

## Existing translations
You can track the status of the existing translations on the [translations status](https://www.worldcubeassociation.org/translations/status) page.

| Language | Translator | GitHub Account |
| -------- | ---------- | -------------- |
| Catalan | Ivan Ros | @Roco033 |
| Chinese-Simplified (zh-CN)| Baiqiang Dong, Lee Yiu Sing | @Baiqiang @PikAsriel |
| Chinese-Traditional (zh-TW) | Rui-Jun Liu, Han Wu | @hanwu85 |
| Croatian | Dominik Vidaković |
| Czech | Kristýna Vaštová, Emma Beranová | @beranka |
| Danish | Daniel Edgal |
| Dutch | Ron van Bruchem, Heleen van der Ree |
| Finnish | Timo Norrkniivilä |
| French | Philippe Virouleau | @viroulep |
| German | Laura Ohrndorf, Annika Stein | @Laura-O @AnnikaStein |
| Hungarian | Niki Placskó |
| Indonesian | Hafizh Dary Faridhan Hudoyo | @HafizhHD |
| Italian | Simone Cantarelli | @SimoneCantarelli |
| Japanese | Kotaro Terada, Yuki Tanaka | @kotarot @yktanaka |
| Korean | Ilkyoo Choi |
| Polish | Jonatan Kłosko | @jonatanklosko |
| Portuguese-Brazilian | Pedro Santos Guimarães | @pedrosino |
| Portuguese-European | António Gomes | @cubizh |
| Romanian | Teodor Toma |
| Russian | Oleg Gritsenko | @Claster |
| Slovakian | Anton Mozola |
| Slovenian | Jernej Omulec |
| Spanish-European | Raúl Morales Hidalgo and Carlos Méndez García-Barroso | @moralsh and ? |
| Swedish | Viktor Zenk | @viktorzenk |
| Ukrainian | Roman Ostapenko | |
| Vietnamese | Trung Vương Thiện | @trungams |

## WIP Translations

There are no translations in progress at the moment.

## General notes

Although there are around 500 keys to translate, most of them are very simple and the overall number of actual words to translate is around 2500.
Compared to the 10000+ words of the Regulations and Guidelines, it should be pretty easy to handle ;)

You don't need knowledge about git, github, or anything about the website internals to be able to translate the website.
However it will be easier for you if you are already familiar with the public pages of the WCA website!

Everything is stored in one big file per language that is called a "locale file", it's equivalent to a dictionary.
We have a dedicated application to help you with the translation process ("Internationalize", with a tutorial in the second part of this page), and basically you can just email the software team the resulting locale file after the translation, with your language name in your language.

If you know how to submit a pull-request (PR), feel free to do so :)
[Here](https://github.com/thewca/worldcubeassociation.org/pull/1105) is an example of such PR.

If you have any questions or feedback about this, feel free to contact the software team!

## Dealing with special keys

Before you rush into the application to happily translate the locale file, here is some basic information about how translations work, **please read them carefuly**!

When we want to output a translated sentence (also known as *string*) on the website, we use a reference *key* that is then used by our application to get the actual text in the language of the user, if available.
For example if we want to show the title of the Competitions index page, we use the key `competitions.index.title` that then gets replaced by "Competitions" if the language is English, or "Compétitions" if the language is French.

Most of the keys need translation as simple as that, however some of them are slightly more complicated and are worth explaining.
There are basically 3 things you need to be careful about, and they are described in the next 3 subsections.

### Using variables

Sometimes translated strings contain variables. For example let's look at the waiting list message displayed when your registration at a competition is pending:

```yaml
waiting_list: "You are currently number %{i} of %{n} on the waiting list."
```

In this example, `%{i}` will be replaced by your position in the waiting list, and `%{n}` will be replaced by the total number of people in the waiting list.
When translating this you can move them around to where it fits best your language, just remember to use them ;)

Just putting here the French translation to display an example:

```yaml
waiting_list: "Votre inscription est en attente, vous êtes en position %{i} sur %{n}."
```

As you can see, the variable names (`i` and `n`) stay the same: please **do not** change the variable names!

### Using html

Sometimes translated strings will contain html (this is the language used to format text in your browser). It's usually used on a specific part of the sentence, so please keep the html on the same part of your translation.
For example the hint about name given to a user when registering is the following:

```yaml
name: "Enter your full name correctly, for example <strong>Stefan Pochmann</strong>. Not sloppily like <strong>s pochman</strong>."
```

Here `<strong></strong>` is use to put the examples in bold, when translating the string please also use `<strong></strong>` on your example names.
On this particular example, you should also feel free to edit the examples to what make most sense in your language/culture.
For example in France the capitalization mistake **Stefan POCHMANN** is more present than others, so the French translation looks like:

```yaml
name: "Entrez votre nom complet correctement, par exemple <strong>Stefan Pochmann</strong>. Pas négligemment comme <strong>s pochman</strong> ou <strong>Stefan POCHMAN</strong>."
```

Note: some strings may contain both variables and html.
Here is an example with the registrations opening message:

```yaml
will_open_html: "Registration will open in <strong>%{days}</strong> on %{time}."
```



### Dealing with pluralization

The last tricky kind of keys is the one used to pluralize words.
Pluralization rules depend entirely on the language, so the website has a flexible approach to handle it (and knows how to handle it for every language); lets take the example of the pluralization of "day(s)", which has the key `days`:

```yaml
days:
  one: "1 day"
  other: "%{count} days"
```

In English, if the number of days is "1", you want to display "1 day", otherwise you want to display "%{count} days", with `count` being replaced by the actual number of days.

But in other languages the rule may be different, for example the same key in French looks like this:

```yaml
days:
  zero: "0 jour"
  one: "1 jour"
  other: "%{count} jours"
```
(we don't pluralize "word" if there is no "word")

In Jonatan's application such keys are detected and it will automatically enforce the correct set of keys to be used, based on the target language.


## "Internationalize"

"[Internationalize](https://internationalize.jonatanklosko.com/)" is a small application written by Jonatan to help through the translation of the website locale file.

Here is a small tutorial/walk-through for the features you will most likely use.

### Getting started

Go on the [website](https://internationalize.jonatanklosko.com/) of the application and sign up there.

When signed in, you should be able to create a new translation by clicking on "New translation":

![new translation view](/assets/images/new_translation_view.png)

Here are the descriptions of the field:

1. The name of your translation, anything really
2. The base locale code, in our case it will always be "en".
3. The locale code for the language you're translating (see [here](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes), the "639-1" column). It may be completed if your language is based on another one (eg: "pt" is used for Portuguese, "pt-BR" is used for Brazilian Portuguese).
4. The url of the file you're translating, in our case we use "https://raw.githubusercontent.com/thewca/worldcubeassociation.org/main/config/locales/en.yml" as it will always point to the most up-to-date version of the English locale.
5. The url of the locale file in the target language. If your language doesn't exist yet on the website **leave it empty**! If you're updating an existing translation, you can import the work done until now by specifying the locale url (change "en" by your locale code in the link used for 4).
6. If the generated file should contain include hashes of the original keys. **Keep it checked!** It will enable the website to display accurate information about the translation status in the [translations status](https://www.worldcubeassociation.org/translations/status) page :)

Click create once, and you should see your newly created translation under "My Translations". Click on it to start the translation process.

You should now be on the translate view:

![translate view](/assets/images/translate_view.png)

Some description:

1. Status bar
2. The current key hierarchy in the file, the last name is the name of the key!
3. A message about the context in which the key is used on the website.
4. The original English value.
5. This is where you work and put the translation in ;)
6. Click "save" to save the current translation and proceed to the next one.
7. If you're unsure about how to translate the key or want to translate it later you can skip it.
8. If you want to keep the English version (such as for "WCA ID" for example), please use this button and do **not** skip the key.

Basically you can enter your translation and press "enter": it will have the same effect as the "save" button and keep the focus on the "translation" field. That way you can "translate, enter; translate, enter;..." and make the whole process very fast!

If you want to change the translation for a key you already translated you can use the "Browse" view:

![browse view](/assets/images/browse_view.png)

For example the key hierarchy on the previous screen is "fr > countries > XA", so you can click on "countries" and find the "XA" key.

When you're done translating (or at any time), you can download the resulting file through the menu:

![menu view](/assets/images/menu_view.png)

Then you just have to send it (with your language name in your language) to the software team and voilà!


The website evolves rapidly, so you may want to keep the translation up to date regularly.
The next subsection covers synchronization.

### Synchronizing/Updating a remote locale

The website has a [translations status](https://www.worldcubeassociation.org/translations/status) page, where you can find precise information about translations.
It displays *missing* keys (that have been added after the last translation), as well as *outdated* keys (that have been changed in English since the last translation).

To synchronize your translation with the English one, you can use the "Synchronize with the remote" feature, which is available in the menu displayed in the image above.

Before actually doing the sync, you will be prompted with a summary of the changes, and asked to resolve any conflict that may arise.
Conflicts happen when an existing key has been modified in the English locale, after you translated it.
For example the event names changed recently, and this is what I had when synchronizing the French locale:

![sync view](/assets/images/sync_view.png)

If any key has been added, you need to go back to the translation process!

After you're done with filling the missing parts, follow these steps:

1. Copy the YAML file that the app generates. The most efficient way of doing this is clicking on the menu icon and selecting "Show raw", then you can just click "Copy to clipboard". No need for downloading a file.
2. Go to this page: https://www.worldcubeassociation.org/translations/edit. Select your locale and paste the copied file content.
3. Click the button.

This automatically creates a Pull Request on GitHub, and after all necessary automatic checks passes, someone from the Software Team merges this and it comes live with the next deployment.

### A note about pluralization

As you will see by using the app, the keys used for the pluralization of a word or a sentence are enforced based on the target language.
There is one specificity that is worth knowing: the key `zero` is always required!
In some case it's nicer to be able to override the default value with a nicer "zero" case, eg use "no objects" instead of "0 objects". This is an optional key in the website framework, however we made it required in the app, because it was way easier for us.
This will lead to some confusing cases:

![plural view](/assets/images/plural_view.png)

In this particular case, it makes absolutely no sense to provide a `zero` key, since the message is never displayed if there is no registration.
In such cases you can just copy the value of the `other` key.
There is a bunch of them, sorry for this!

## Bonus: Translating non WCA-specific parts of the application

Some of the content is provided by external modules (such as some error messages, or the sign in/up system), most of these modules already have their own translation, so basically you shouldn't have anything to do here.

If you don't plan on opening a PR, please skip the next information and we'll be happy to do this for you!

Here is where you can get the additional modules' translation:
 - `time_will_tell`: it's the component used to display date ranges, such as on the competitions index page. There is no locale available, so if you want to translate these too, you need to take [this](https://github.com/thewca/worldcubeassociation.org/blob/main/config/locales/time_will_tell.en.yml) file, translate it, and include the translation in your PR.
 - `carrierwave`: it's the tool used to update profile pictures. There are plenty of locales already available [here](https://github.com/carrierwaveuploader/carrierwave-i18n/blob/3046d86ef580d449ea6ad70aab05db22276da01c/rails/locales). Pick the one for your language, edit the key names containing "whitelist" or "blacklist" to "white_list" or "black_list" (respectively), and include it in your PR.
 - `momentjs`: it's the component used to display a calendar when one has to fill in a date. Here again plenty of locales are available, so you just need to activate your language in [this](https://github.com/thewca/worldcubeassociation.org/blob/34d46d3f0ed022ec20a84d6d6241853f05495b71/WcaOnRails/app/assets/javascripts/application.js#L27-L28) file (just as I did with the French one).

## Bonus: Dev notes

Just skip this part if you don't intend to mess with i18n internal in the website.

### Adding a new language
#### Adding verified translators

Currently, this must be done with the Rails console. Find the i18n code for the relevant lanugage (in this example, we use Hindi: `hi`)
- Add Group Metadata for the new translator UserGroup we'll create (and assign it to a variable so we can reference it in the next command): 
```rails
hindi = GroupsMetadataTranslators.create(locale: 'hi')
```

- Add the UserGroup itself
```rails
UserGroup.create(name: 'Hindi', group_type: 'translators', is_active: true, is_hidden: false, metadata: hindi)
```

Once this is done, you can add verified translators for the new language from the (Translators panel)[https://www.worldcubeassociation.org/panel/admin#translators]

#### To activate a locale:

New information:
We only add a locale to the relevant files once we have a translation available for it from Internationalize- otherwise it becomes available in the public-facing locale selector.

See (this PR)[https://github.com/thewca/worldcubeassociation.org/pull/10986/files] for a reference of adding a new translation

Old information (elements may still be relevant): 
- Add the locale with the language information to the available locales (in `WcaOnRails/config/locales/locales.rb`)
- Activate the `fullCalendar`/`moment` locale in the `WcaOnRails/app/assets/javascripts/application.js` (look at the numerous examples)

You can check missing translations by running `i18n-tasks missing` in the `WcaOnRails` directory.

5 translations are expected to be missing (those under 'datepicker' or 'timepicker') for every language but English.

#### Language codes vs region codes

In most cases, language code and region code will be the same. However, it is important to know that these two different types of code exist. Most places in our codebase will only reference the language code, but in some places (eg `locales.rb`) both will appear:

```yml
en: {
      flag_id: "gb",
      name: "English",
    },
```

In this case, `en` is the _language_ code, while `flag_id` is _region_ code, which may have a different ISO code - as is the example with English (the language), where the Great Britain flag is used to represent it. (This is a good illustration of why using flags to represent languages is not generally not a good idea.)

### Translations status internals

We display the status of each translation on the [translations status](https://www.worldcubeassociation.org/translations/status) page.
We determine if translations are outdated by looking at the hash of the original value attached to them; the Internationalize app adds an "original_hash" comment, containing the first 7 chars of the sha1 sum of the original value:
```yaml
pt-BR:
  countries:
    #original_hash: 3f8c9de
    XA: Múltiplos Países (Ásia)
    #original_hash: 090f3ce
    XE: Múltiplos Países (Europa)
```

We compare the original hash associated to the key to the one computed with the current English text for the same key, if they don't match the translation is outdated.
