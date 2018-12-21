# Localization

- [မိတ်ဆက်](#introduction)
- [Defining Translation Strings](#defining-translation-strings)
    - [Using Short Keys](#using-short-keys)
    - [Using Translation Strings As Keys](#using-translation-strings-as-keys)
- [Retrieving Translation Strings](#retrieving-translation-strings)
    - [Replacing Parameters In Translation Strings](#replacing-parameters-in-translation-strings)
    - [Pluralization](#pluralization)
- [Overriding Package Language Files](#overriding-package-language-files)

<a name="introduction"></a>
##  မိတ်ဆက်

သင့်ရဲ့ Application အတွင်းမှာ ဘာသာစကားများစွာ လွယ်လွယ်ကူကူ ထည့်သွင်းလို့ရစေရန်၊ ဘာသာစကားအမျိုးမျိုးဖြင့် ရေးသားထားသော string တွေကို အလွယ်တကူ retrieve လုပ်နိုင်အောင် laravel ရဲ့ localization feature ကထောက်ပံ့ပေးထားပါတယ်။ `resources/lang` လမ်းကြောင်း အောက်မှာရှိတဲ့ file တွေထဲမှာ ဘာသာစကား စကားစုတွေကို သိမ်းဆည်းကြရပါတယ်။ ထိုလမ်းကြောင်းအောက်မှာ ကိုယ့် Application မှာထည့်သွင်းချင်တဲ့ ဘာသာစကားတစ်ခုချင်းစီအတွက် folder အသီးသီးဆောက်ပြီး ဆောက်ပြီး အသုံးပြုရမှာဖြစ်ပါတယ်။

    /resources
        /lang
            /en
                messages.php
            /es
                messages.php

ဘာသာစကားကြိုတင်သတ်မှတ်ထားသော ဖိုင်တွေဆီမှ keyed strings တွေပါတဲ့ array return ပြန်လာပါတယ်။ ဥပမာ -

    <?php

    return [
        'welcome' => 'မင်္ဂလာပါ'
    ];

### ဘာသာစကားညွှန်ကိန်း(locale) များကို အစီအစဉ်ချခြင်း

သင့် application အတွက် ပုံသေ ဘာသာစကားကို `config/app.php` configuration ဖိုင်ထဲမှာ သတ်မှတ်ထားပါတယ်။ ၎င်းကို သင့် application နဲ့ကိုက်ညီအောင် ပြောင်းလဲနိုင်ပါတယ်။ Runtime တွင်လဲ `setLocale` method ကို `App` facade နဲ့တွဲဖက်ပြီး ဘာသာစကားများ ပြောင်းလဲအသုံးပြုနိုင်ပါတယ်။

    Route::get('welcome/{locale}', function ($locale) {
        App::setLocale($locale);

        //
    });

လက်ရှိသတ်မှတ်ထားတဲ့ ဘာသာပြန်မည့်စကားမှာ မပါဝင်သော စကားစုများအတွက် အသုံးပြုနိုင်ရန် အရံဘာသာစကား (fallback language) ကိုလည်း သတ်မှတ်ပေးဖို့လိုပါလိမ့်မယ်။ "ပုံသေဘာသာစကား" သတ်မှတ်သကဲ့သို့ပင် "အရံဘာသာစကား" ကိုလဲ `config/app.php` configuration ဖိုင်ထဲမှာသတ်မှတ်နိုင်ပါတယ်။

    'fallback_locale' => 'en',

#### Determining The Current Locale

`getLocale` နှင့် `isLocale` method တို့ကို `App` facade တွင်အသုံးပြုပြီး လက်ရှိအသုံးပြုနေသော ဘာသာစကားကို ဆုံးဖြတ်ခြင်း (သို့) ထို ဘာသာစကားညွှန်းကိန်းသည် ကြိုတင်သတ်မှတ်ထားခြင်းရှိမရှိ စစ်ဆေးခြင်းများပြုလုပ်နိုင်ပါတယ်။

    $locale = App::getLocale();

    if (App::isLocale('en')) {
        //
    }

<a name="defining-translation-strings"></a>
## Defining Translation Strings

<a name="using-short-keys"></a>
### Using Short Keys

Typically, translation strings are stored in files within the `resources/lang` directory. Within this directory there should be a subdirectory for each language supported by the application:

    /resources
        /lang
            /en
                messages.php
            /es
                messages.php

All language files return an array of keyed strings. For example:

    <?php

    // resources/lang/en/messages.php

    return [
        'welcome' => 'Welcome to our application'
    ];

<a name="using-translation-strings-as-keys"></a>
### Using Translation Strings As Keys

For applications with heavy translation requirements, defining every string with a "short key" can become quickly confusing when referencing them in your views. For this reason, Laravel also provides support for defining translation strings using the "default" translation of the string as the key.

Translation files that use translation strings as keys are stored as JSON files in the `resources/lang` directory. For example, if your application has a Spanish translation, you should create a `resources/lang/es.json` file:

    {
        "I love programming.": "Me encanta programar."
    }

<a name="retrieving-translation-strings"></a>
## Retrieving Translation Strings

`__` ဆိုတဲ့ helper function အသုံးပြုပြီးတော့ ဘာသာစကား files တွေမှ စကားစုများထုတ်ယူနိုင်ပါတယ်။ `__` method က ဘာသာပြန်မည့်စကားစုတွေရဲ့ ဖိုင်နာမည် နဲ့ key တွေကို သူ့ရဲ့ ပထမ argument အနေနဲ့ လက်ခံပါတယ်။ ဥပမာ `resources/lang/messages.php` ဘာသာစကားဖိုင်အတွင်းမှ `welcome` စကားစုကို ရယူမည်ဆိုပါစို့ -

    echo __('messages.welcome');

    echo __('I love programming.');

သင်က [Blade templating engine](/docs/{{version}}/blade) ကိုသုံးနေတာဆိုရင် ဘာသာစကားစုတွေကို ဖော်ပြရန်(echo) အတွက် `{{ }}` syntax သို့မဟုတ် `@lang` directive ကိုအသုံးပြုရပါမယ်။

    {{ __('messages.welcome') }}

    @lang('messages.welcome')

အကယ်လို့ သတ်မှတ်လိုက်တဲ့ ဘာသာစကားစု မရှိခဲ့လျှင် `__` function က ဘာသာပြန်မည့်စကားစု၏ key ကိုပဲ return ပြန်ပေးပါတယ်။ ထို့ကြောင့် အထက်ကဖော်ပြထားသော ဥပမာတွင် ဘာသာပြန်မည့်စကားစု မရှိခဲ့လျှင် `__` function က `messages.welcome` ကို return ပြန်ပေးပါလိမ့်မယ်။

> {note} The `@lang` directive does not escape any output. You are **fully responsible** for escaping your own output when using this directive.

<a name="replacing-parameters-in-translation-strings"></a>
### Replacing Parameters In Translation Strings

သင်ဆန္ဒရှိလျှင် သင့်ရဲ့ ဘာသာပြန်မည့်စကားစုတွေအတွင်းမှာ place-holder တွေ သတ်မှတ်နိုင်ပါတယ်။ place-holder အားလုံးက ရှေ့မှာ `:` ခံထားပါတယ်။ ဥပမာအားဖြင့် သင့်ရဲ့ welcome message ကို place-holder name ထည့်သွင်းပြီး သတ်မှတ်မည်ဆိုလျှင် -


    'welcome' => 'Welcome, :name',

စကားစုတွေကိုထုတ်ယူသောအခါ place-holder များနေရာတွင်အစားထိုးရန်အတွက် `__` function ၏ ဒုတိယ argument အနေနဲ့ အစားထိုးချင်သည့် array ကိုထည့်ပေးလိုက်ရပါမယ်။

    echo __('messages.welcome', ['name' => 'dayle']);

အစားထိုးဝင်ရောက်လာသောတန်ဖိုးများသည် place-holder ရဲ့ စကားလုံး အကြီးအသေး ထားသိုပုံအလိုက် ပြောင်းလဲသွားမှာဖြစ်ပါတယ်။

    'welcome' => 'Welcome, :NAME', // Welcome, DAYLE
    'goodbye' => 'Goodbye, :Name', // Goodbye, Dayle

<a name="pluralization"></a>
### အများကိန်းပြုလုပ်ခြင်း

ဘာသာစကားအမျိုးမျိုးမှာ အများကိန်းပြောင်းရန် ရှုပ်ထွေးသော စည်းမျဉ်းစည်းကမ်းတွေများစွာရှိသည့်အတွက် အများကိန်းပြောင်းခြင်းဟာ ရှုပ်ထွေးတဲ့ပြဿနာ ဖြစ်လာပါတယ်။ "pipe" character ကိုသုံးခြင်းအားဖြင့် စကားစုများတွင် အများကိန်း နှင့် အနဲကိန်းကို ခွဲခြား အသုံးပြုနိုင်ပါတယ်။

    'apples' => 'There is one apple|There are many apples',

များပြားသောကိန်းဂဏန်းအမျိုးမျိုးတို့အတွက် ဘာသာပြန်မှုပြုလုပ်နိုင်ရန် ပိုမိုရှုပ်ထွေးသော အများကိန်းပြောင်းသည့် စည်းမျဉ်းများအားလည်း ဖန်တီးနိုင်ပါသေးတယ်။

    'apples' => '{0} There are none|[1,19] There are some|[20,*] There are many',

သင့်ရဲ့ စကားစုကို အများကိန်းပြုလုပ်ခြင်းအခြေအနေဖြင့် အသုံးပြုမည်ဟုရွေးခြယ်ထားပြီးနောက် ပေးလိုက်သော "count" ပေါ်မူတည်၍  စကားစုကိုထုတ်ယူသောအခါ `trans_choice` function ကိုအသုံးပြုနိုင်ပါတယ်။ ယခု ဥပမာတွင် count ၏တန်ဖိုး ၁ ထက်ကြီးတာနဲ့ ဘာသာပြန်မည့်စကားစုက အများကိန်းပုံစံဖြင့် return ပြန်တာမှာဖြစ်ပါတယ်။

    echo trans_choice('messages.apples', 10);

You may also define placeholder attributes in pluralization strings. These placeholders may be replaced by passing an array as the third argument to the `trans_choice` function:

    'minutes_ago' => '{1} :value minute ago|[2,*] :value minutes ago',

    echo trans_choice('time.minutes_ago', 5, ['value' => 5]);

If you would like to display the integer value that was passed to the `trans_choice` function, you may use the `:count` placeholder:

    'apples' => '{0} There are none|{1} There is one|[2,*] There are :count',

<a name="overriding-package-language-files"></a>
## Overriding Package Language Files

Laravel နဲ့အတူ တွဲစပ်အသုံးပြုနိုင်တဲ့ packages တွေမှာ သူတို့ရဲ့ ကိုယ်ပိုင် ဘာသာစကားဖိုင်တွေတစ်ပါတည်းပါလာပါတယ်။ အဲ့ဒီဖိုင်တွေကို change ဖို့ packages တွေရဲ့ မူရင်းဖိုင်တွေကို သွားပြင်နေမဲ့အစား `resources/lang/vendor/{package}/{locale}` လမ်းကြောင်းအောက်ကနေတစ်ဆင့် override ပြုလုပ်နိုင်ပါတယ်။

ဥပမာ `skyrim/hearthfire` လို့ အမည်တွင်တဲ့ package အတွက် `messages.php` ဖိုင်ထဲမှာရှိတဲ့ English Language ကို override လုပ်ချင်တယ်ဆိုရင် `resources/lang/vendor/hearthfire/en/messages.php` ဖိုင်ကနေတစ်ဆင့် ပြုလုပ်နိုင်ပါတယ်။ Override လုပ်ဖို့လိုအပ်တဲ့ ဘာသာပြန်မည့်စကားစုတွေကိုပဲ အဲ့ဒီဖိုင်ထဲမှာသတ်မှတ်ထားဖို့လိုအပ်ပါတယ်။ ကျန်တဲ့စကားစုအားလုံးကိုတော့ package ရဲ့ language ဖိုင်ထဲက နေပဲ အလုပ်လုပ်သွားမှာဖြစ်ပါတယ်။
