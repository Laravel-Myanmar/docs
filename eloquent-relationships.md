# Eloquent: Relationships

- [မိတ်ဆက်](#introduction)
- [Relationship များ သတ်မှတ်ခြင်း](#defining-relationships)
    - [One To One](#one-to-one)
    - [One To Many](#one-to-many)
    - [One To Many (Inverse)](#one-to-many-inverse)
    - [Many To Many](#many-to-many)
    - [Has Many Through](#has-many-through)
- [Polymorphic Relationships](#polymorphic-relationships)
    - [One To One](#one-to-one-polymorphic-relations)
    - [One To Many](#one-to-many-polymorphic-relations)
    - [Many To Many](#many-to-many-polymorphic-relations)
    - [Custom Polymorphic Types](#custom-polymorphic-types)
- [Querying Relations](#querying-relations)
    - [Relationship Methods Vs. Dynamic Properties](#relationship-methods-vs-dynamic-properties)
    - [Querying Relationship Existence](#querying-relationship-existence)
    - [Querying Relationship Absence](#querying-relationship-absence)
    - [Counting Related Models](#counting-related-models)
- [Eager Loading](#eager-loading)
    - [Constraining Eager Loads](#constraining-eager-loads)
    - [Lazy Eager Loading](#lazy-eager-loading)
- [Inserting & Updating Related Models](#inserting-and-updating-related-models)
    - [The `save` Method](#the-save-method)
    - [The `create` Method](#the-create-method)
    - [Belongs To Relationships](#updating-belongs-to-relationships)
    - [Many To Many Relationships](#updating-many-to-many-relationships)
- [Touching Parent Timestamps](#touching-parent-timestamps)

<a name="introduction"></a>
## မိတ်ဆက်

Database tables တွေဟာ တစ်ခုနဲ့တစ်ခုဆက်နွယ်နေပါတယ်။ ဥပမာအားဖြင့် post တစ်ခုမှာ comment အများကြီးရှိနိုင်ပါတယ် သို့မဟုတ် order တစ်ခုဟာ တင်တဲ့ user နဲ့ ပဲသက်ဆိုင်ပါတယ်။ Eloquent က အရှေ့မှာဆွေးနွေးခဲ့တဲ့ relationship တွေကို စီမံခန့်ခွဲရတာ လွယ်ကူစေပါတယ်, ပြီးတော့ relationship အမျိုးအစားများစွာကိုလည်း ထောက်ပံ့ပေးထားပါတယ်။

<div class="content-list" markdown="1">
- [One To One](#one-to-one)
- [One To Many](#one-to-many)
- [Many To Many](#many-to-many)
- [Has Many Through](#has-many-through)
- [One To One (Polymorphic)](#one-to-one-polymorphic-relations)
- [One To Many (Polymorphic)](#one-to-many-polymorphic-relations)
- [Many To Many (Polymorphic)](#many-to-many-polymorphic-relations)
</div>

<a name="defining-relationships"></a>
## Relationships များသတ်မှတ်ခြင်း

Eloquent relationships တွေကို model classes တွေမှာ methods များအဖြစ်နဲ့ သတ်မှတ်နိုင်ပါတယ်။  Eloquent models တွေလိုပါပဲ Eloquent Relationship တွေဟာ query တွေကို တည်ဆောက်တဲ့ နေရာ [query builders](/docs/{{version}}/queries) မှာ အစွမ်းထက်တဲ့အတွက် Relationship တွေကို model classes တွေမှာ သတ်မှတ်ပြီးတဲ့ အခါ method chaining လိုမျိူး အရမ်းအသုံးဝင်တဲ့ query ပြုလုပ်နိုင်စွမ်းကို ရရှိနိုင်မှာဖြစ်ပါတယ်။ ဥပမမာဆိုရလျှင်, `user` နဲ့ `post` relationship မှာ constraints တွေထပ်ပြီးထည့်လို့ရနိုင်ပါတယ်။

    $user->posts()->where('active', 1)->get();

ဒါပေမဲ့ အရှေ့ကိုဆက်ပြီးမလေ့လာခင်မှာ, Relationship Type တစ်ခုချင်းစီကို ဘယ်လိုသတ်မှတ်လဲဆိုတာ လေ့လာကြည့်ရအောင်

<a name="one-to-one"></a>
### One To One (တစ်ခု မှ တစ်ခု)

A one-to-one relationship (တစ်ခု မှ တစ်ခု ဆက်နွယ်မှု) ဆိုတာ အခြေခံအကျဆုံး relationship ပါပဲ။ ဥပမာ၊ `User` တစ်ယောက်မှာ `Phone` တစ်လုံးပဲရှိတယ် ဆိုကြပါစို့။ ဒါကို Eloquent Relationship အနေနဲ့သတ်မှတ်ရရင် `User` ဆိုတဲ့ model class ထဲမှာ `phone` ဆိုတဲ့ method ကို သတ်မှတ်ပေးရပါမယ်။ `phone` method ထဲမှာ `hasOne` ဆိုတဲ့ method ကို ခေါ်ပြီး return ပြန်ပေးရပါမယ်။

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Get the phone record associated with the user.
         */
        public function phone()
        {
            return $this->hasOne('App\Phone');
        }
    }

`hasOne` method ထဲကို ပထမဆုံးပို့ပေးရမယ့် argument က အခု model နဲ့ ဆက်နွယ်နေတဲ့ model ရဲ့ နာမည်ပါ။ ဥပမာ အခု model ဟာ user model ဖြစ်တဲ့အတွက် user နဲ့ ဆက်နွယ်တဲ့ phone model ရဲ့ နာမည်ကို ပို့ပေးရပါမယ်။ Relationship သတ်မှတ်ပြီးတာနဲ့ Eloquent ရဲ့ dynamic properties တွေကိုအသုံးပြုပြီး သက်ဆိုင်တဲ့ record တွေကို ထုတ်ယူလို့ရပါပြီ။ Dynamic Properties တွေဟာ Relationship method တွေကို model ရဲ့ property တွေလို အသုံးပြုစေနိုင်ပါတယ်။ ဥပမာ user model ရဲ့ relationship method ဖြစ်တဲ့ phone() ကို dynamic property အဖြစ် အသုံးပြုတာကို ဖော်ပြပါမယ်။

    $phone = User::find(1)->phone;

Eloquent က relationship တစ်ခုမှာ ဘယ်ဟာက foreign key လည်းဆိုတာ model name ကို ကြည့်ပြီးဆုံးဖြတ်ပါတယ်။ အခု ဥပမာမှာဆိုလျှင် `Phone` model မှာ `user_id` ဆိုတဲ့ foreign key ပါမယ်လို့ အလိုအလျှောက်ယူဆပါတယ်။ အကယ်၍ foreign key ကို မိမိစိတ်ကြိုက် သတ်မှတ်လိုပါက `hasOne` method မှာ ဒုတိယ argument ပေးပြီး သတ်မှတ်နိုင်ပါတယ်။

    return $this->hasOne('App\Phone', 'foreign_key');

Eloquent က Foreign Key တစ်ခုဟာ Parent ရဲ့ `id` သို့မဟုတ် `$primarykey` column နဲ့ တူညီမှုတစ်ခု ရှိသင့်တယ်လို့ ယူဆပါတယ်။ တစ်နည်းအားဖြင့်ဆိုရလျှင်, Eloquent ဟာ user ရဲ့ `id` column နဲ့ တူညီတဲ့ တန်ဖိုးတွေကို `Phone` ရဲ့ `user_id` column မှာပဲ ရှာမှာဖြစ်ပါတယ်။ တစ်ကယ်လို့ parent ရဲ့ `id` နဲ့ Relationship မပြုလုပ်လိုလျှင် `hasOne` method ကို တတိယ argument ပို့ပေးပြီး မိမိကြိုက်နှစ်သက်ရာ သတ်မှတ်နိုင်ပါတယ်။

    return $this->hasOne('App\Phone', 'foreign_key', 'local_key');

#### Defining The Inverse Of The Relationship
#### (Relationship ကို ပြောင်းပြန်သတ်မှတ်ခြင်း)

အခုဆိုရင် `User` model မှတစ်ဆင့် `Phone` model ကို ဝင်ရောက်လို့ရပါပြီ။ ဒါဆိုရင် `Phone` model ကနေပြီးတော့ `User` ကို ပြန်လည်ဆက်သွယ်ပေးနိုင်မယ့် Relationship တစ်ခု သတ်မှတ်ကြည့်ရအောင်။ ဒီ `Phone` ကို ဘယ် `User` က ပိုင်တာလည်းဆိုတာမျိုး။ အဲဒီအတွက်  `hasOne` method ရဲ့ ပြောင်းပြန် `belongsTo` method ကို သတ်မှတ်ပေးနိုင်ပါတယ်။

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Phone extends Model
    {
        /**
         * Get the user that owns the phone.
         */
        public function user()
        {
            return $this->belongsTo('App\User');
        }
    }

အထက်က ဥပမာမှာ Eloquent ဟာ `Phone` model ရဲ့  `user_id` column ထဲက တန်ဖိုးနဲ့ `User` model ရဲ့ `id` column ထဲက တန်ဖိုးတွေကို တိုက်ပြီးရှာပါမယ်။ Eloquent က Relationship method ကို ကြည့်ပါတယ်။ ပြီးလျှင် method နာမည်ရဲ့ နောက်မှာ `_id` ကို ထည့်ပြီး default foreign key name အဖြစ် သတ်မှတ်ပါတယ်။ ဒါပေမယ့် `Phone` model ရဲ့ foreign key ဟာ `user_id` မဟုတ်ခဲ့ဘူးဆိုလျှင်တော့ `belongsTo` method မှာ မိမိသတ်မှတ်ထားတဲ့ foreign key နာမည် ကို ဒုတိယ argument အနေနဲ့ထည့်ပေးနိုင်မှာတယ်။

    /**
     * Get the user that owns the phone.
     */
    public function user()
    {
        return $this->belongsTo('App\User', 'foreign_key');
    }

တစ်ကယ်လို့ Parent Model က `id` ကို primary key အဖြစ်မသုံးထားလျှင် ဒါမှမဟုတ် `id` မဟုတ်တဲ့ အခြား column တစ်ခုနဲ့ child model ကို ချိတ်ဆက်စေလိုလျှင် `belongsTo` method ထဲကို မိမိ အသုံးပြုလိုတဲ့ column နာမည်ကို တတိယ argument အနေနဲ့ ပို့ပေးနိုင်ပါတယ်။

    /**
     * Get the user that owns the phone.
     */
    public function user()
    {
        return $this->belongsTo('App\User', 'foreign_key', 'other_key');
    }

<a name="one-to-many"></a>
### One To Many (တစ်ခု မှ အများ)

Model တစ်ခုမှာ တစ်ခြား Model ဟာ တစ်ခုထက်မက မှီခိုနေတဲ့ အခြေအနေမျိုးမှာ One To Many Relationship (တစ်ခု မှ အများ ဆက်နွယ်မှု)ကို သတ်မှတ်ရပါမယ်။ ဥပမာ blog post တစ်ခုမှာ comment တွေအများကြီးရှိနေနိုင်တဲ့ အဖြစ်မျိုးပါပဲ။ အခြား Eloquent Relationship တွေလိုပါပဲ, one to many relationship ကို သတ်မှတ်ချင်ရင် Eloquent Model မှာ function တစ်ခုရေးပေးရပါမယ်။  

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Post extends Model
    {
        /**
         * Get the comments for the blog post.
         */
        public function comments()
        {
            return $this->hasMany('App\Comment');
        }
    }

ဒီနေရာမှာ သတိပြုရမှာက Eloquent ဟာ `Comment` model မှာ ဖြစ်နိုင်မယ့် foreign key column ကို အလိုအလျောက် ဆုံးဖြတ်မှာ ဖြစ်ပါတယ်။ Eloquent ဟာ `snake case` လို့ခေါ်တဲ့ နာမည်သတ်မှတ်ခြင်းကို အသုံးပြုပြီး one to many မှာ many ဖြစ်တဲ့ model ရဲ့ column တွေမှာ one ဖြစ်တဲ့ model နာမည်နောက်မှာ `_id` ပေါင်းထည့်ပြီး foreign key သတ်မှတ်ခြင်းကို ဆုံးဖြတ်မှာဖြစ်ပါတယ်။ အခု ဥပမာ မှာဆိုရင် `Comment` model ရဲ့ foreign key ဟာ `post_id` ဖြစ်တယ်လို့ သတ်မှတ်မှာပဲဖြစ်ပါတယ်။

Relationship သတ်မှတ်ပြီးတာနဲ့ post တစ်ခုမှာ ရှိတဲ့ comment တွေကို ထုတ်ယူနိုင်ပါပြီ။ Eloquent ဟာ dynamic preperties ဆိုတဲ့ feature ရှိတဲ့အတွက် သတ်မှတ်ထားတဲ့ Relationship method တွေကို model ရဲ့ Property အနေနဲ့ အလွယ်တကူ အသုံးပြုနိုင်ပါတယ်။

    $comments = App\Post::find(1)->comments;

    foreach ($comments as $comment) {
        //
    }

Relationships တွေဟာ query တွေတည်ဆောက်ဖို့ အတွက် အသုံးပြုထာတာဖြစ်တဲ့အတွက် comments တွေကို ထုတ်ယူတဲ့အခါမှာ အခြား constraint တွေကို ထပ်ထည့်နိုင်ပါတယ်။ Constraint တွေထပ်ထည့်တဲ့အခါမှာ `comments()` method ကိုအသုံးပြုပြီး လိုချင်တဲ့ query ရသည်အထိ condition တွေ chain ပြုလုပ်ပြီး အသုံးပြုရမှာဖြစ်ပါတယ်။

    $comment = App\Post::find(1)->comments()->where('title', 'foo')->first();

`hasOne` method လိုပါပဲ foreign key နဲ့ local key တွေကို မိမိနှစ်သက်ရာပေးလိုလျှင် `hasMany` method ထဲကို argument တွေထည့်ပေးပြီးသတ်မှတ်နိုင်ပါတယ်။ 

    return $this->hasMany('App\Comment', 'foreign_key');

    return $this->hasMany('App\Comment', 'foreign_key', 'local_key');

<a name="one-to-many-inverse"></a>
### One To Many (Inverse)

အခုဆိုရင် post မှတစ်ဆင့် comment တွေကို ယူလို့ရပါပြီ။ comment မှတစ်ဆင့် parent model ဖြစ်တဲ့ post ကို ယူလို့ရအောင် relationship တစ်ခုတည်ဆောက်ကြည့်ရအောင်။ `hasMany` ရဲ့ ပြောင်းပြန် relationship သတ်မှတ်ချင်ရင်, child model မှာ relationship function တစ်ခုကို တည်ဆောက်ပေးရပါမယ်။ ပြီးလျှင် relationship function ထဲမှာ `belongsTo` ဆိုတဲ့ method ကို ခေါ်ပေးရပါမယ်။

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Comment extends Model
    {
        /**
         * Get the post that owns the comment.
         */
        public function post()
        {
            return $this->belongsTo('App\Post');
        }
    }

Relationship သတ်မှတ်ပြီးတာနဲ့ `Comment` တစ်ခုဟာ ဘယ် `Post` အောက်မှာရှိလည်းဆိုတာကို `post` ဆိုတဲ့ "dynamic property" ကိုအသုံးပြုပြီး ထုတ်ယူနိုင်ပါပြီ။

    $comment = App\Comment::find(1);

    echo $comment->post->title;

အထက်ပါ ဥပမာမှာ Eloquent ဟာ `Comment` model ရဲ့ `post_id` ကို `Post` model ရဲ့ `id` နဲ့ တိုက်ညီမှုရှိမရှိစစ်ပါမယ်။ Eloquent ဟာ default foreign key ကို relationship method ရဲ့ နောက်မှာ parent model ရဲ့ primary key နာမည်ကို `_` ပေါင်းထည့်ပြီး သတ်မှတ်ပါတယ်။ ဥပမာ `post_id`။ အကယ်၍ `Comment` model မှာ `post_id` ကို foreign key အဖြစ်မသုံးခဲ့ဘူးဆိုလျှင်, `belongsTo` method ထဲကို မိမိသတ်မှတ်လိုတဲ့ column နာမည်ကို ဒုတိယ argument အနေနဲ့ ထည့်ပေးနိုင်ပါတယ်။

    /**
     * Get the post that owns the comment.
     */
    public function post()
    {
        return $this->belongsTo('App\Post', 'foreign_key');
    }


တစ်ကယ်လို့ Parent Model က `id` ကို primary key အဖြစ်မသုံးထားလျှင် ဒါမှမဟုတ် `id` မဟုတ်တဲ့ အခြား column တစ်ခုနဲ့ child model ကို ချိတ်ဆက်စေလိုလျှင် `belongsTo` method ထဲကို မိမိ အသုံးပြုလိုတဲ့ column နာမည်ကို တတိယ argument အနေနဲ့ ပို့ပေးနိုင်ပါတယ်။

    /**
     * Get the post that owns the comment.
     */
    public function post()
    {
        return $this->belongsTo('App\Post', 'foreign_key', 'other_key');
    }

<a name="many-to-many"></a>
### Many To Many  (အများ မှ အများ)

Many-to-many relations (အများမှအများ ဆက်နွယ်မှု) ဟာ `hasOne` နဲ့ `hasMany` ဆက်နွယ်မှုတွေထက် အနည်းငယ် ပိုရှုပ်ထွေးပါတယ်။ ဥပမာပြရရင် user တစ်ယောက်မှာ role အများကြီးရှိနိုင်သလို role တစ်ခုကိုလည်း user တစ်ယောက်မက ခွဲဝေအသုံးပြုနိုင်သလိုပါပဲ။ ပိုပြီးထပ်ရှင်းရရင် `Admin` ဆိုတဲ့ role မှာ user တစ်ယောက်မက ရှိနိုင်တယ်ဆိုတာမျိုးပါ။ အခု relationship ကိုသတ်မှတ်ဖို့အတွက် `users`, `roles` and `role_user` ဆိုတဲ့ database table သုံးခုလိုအပ်ပါတယ်။ `role_user` table ဟာ Many to Many သတ်မှတ်မယ့် model နှစ်ခုကို အက္ခရာစဥ်လိုက် နာမည်ပေးပြီးတော့ တည်ဆောက်ရမယ့် table ပါ။

Many-to-many relations တွေကို သတ်မှတ်ရန် method တစ်ခုတည်ဆောက်ပေးရပါမယ်။ အဲဒီ method ထဲမှာ `belongsToMany` ကို call ပြီးရလာတဲ့ result ကို return ပြန်ပေးရပါမယ်။ ဥပမာ အနေနဲ့ `User` model မှာ `roles` ဆိုတဲ့ method ကို တည်ဆောက်ကြည့်ရအောင်။

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The roles that belong to the user.
         */
        public function roles()
        {
            return $this->belongsToMany('App\Role');
        }
    }

Relationship သတ်မှတ်ပြီးတာနဲ့ `roles` ဆိုတဲ့ dynamic property ကို အသုံးပြုပြီး user ရဲ့ roles တွေကို ထုတ်ယူလို့ရပါပြီ။

    $user = App\User::find(1);

    foreach ($user->roles as $role) {
        //
    }

တစ်ခြား relationship တွေလိုပါပဲ၊ `roles` method ရဲ့နောက်မှာ method chaining ပြုလုပ်ပြီး query တွေကို တည်ဆောက်လို့ရပါပြီ။

    $roles = App\User::find(1)->roles()->orderBy('name')->get();

အထက်မှာဖာ်ပြခဲ့သလိုပါပဲ table နှစ်ခုရဲ့ ဆက်စပ် table (joining table) နာမည်အတွက် Eloquent ဟာ သက်ဆိုင်ရာ model နှစ်ခုရဲ့ နာမည်တွေကို အက္ခရာစဥ်ပြီး ဆုံးဖြတ်သတ်မှတ်မှာဖြစ်ပါတယ်။ ဒါပေမယ့် joining table ကို မိမိစိတ်ကြိုက်နာမည်ပေးလိုလျှင် `belongsToMany` method ကို ဒုတိယ argument ပေးပြီးသတ်မှတ်နိုင်ပါတယ်။

    return $this->belongsToMany('App\Role', 'role_user');

Joining Table ရဲ့ နာမည်ကို မိမိစိတ်ကြိုက်ပေးရုံတင်မက, column တွေကိုပါ စိတ်ကြိုက် နာမည်ပေးလိုလျှင် `belongsToMany` method ထဲကို arguments တွေထည့်ပြီးပေးလို့ရပါတယ်။ တတိယ argument ဟာ relationship သတ်မှတ်ထားတဲ့ model ရဲ့ foreign key နာမည်ဖြစ်ပြီး, စတုတ္ထ argument ကတော့ ပထမ model နဲ့ ဆက်စပ်မယ့် model ရဲ့ foreign key နာမည်ပဲဖြစ်ပါတယ်။

    return $this->belongsToMany('App\Role', 'role_user', 'user_id', 'role_id');

#### Defining The Inverse Of The Relationship
#### (Relationship ကို ပြောင်းပြန်သတ်မှတ်ခြင်း)

Many-to-many relationship ကို ပြောင်းပြန်သတ်မှတ်လိုလျှင် သက်ဆိုင်ရာ model မှာ နောက်ထပ် `belongsToMany` ကို ထပ်ပြီး call လုပ်ပေးရပါမယ်။ အထက်က user roles ဥပမာအတိုင်းဆက်ပြောရလျှင် `Role` model မှာ `users` method ကိုသတ်မှတ်ပေးရပါမယ်။

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Role extends Model
    {
        /**
         * The users that belong to the role.
         */
        public function users()
        {
            return $this->belongsToMany('App\User');
        }
    }

အခုတွေ့ရတဲ့အတိုင်းပါပဲ ဒီ relationship ဟာ `User` model ဘက်မှာ သတ်မှတ်ထားတာနဲ့ အတူတူပါပဲ။ `belongsToMany` method ထဲကို ထည့်တဲ့ argument ပဲကွာသွားတာကိုတွေ့ရပါမယ်။ `Role` model ထဲက `belongsToMany` မှာ `App\User`ကိုထည့်ပေးရပါမယ်။ `belongsToMany` method ကိုအသုံးပြုထားတဲ့အတွက်ကြောင့် table နဲ့ key တွေကို မိမိစိတ်ကြိုက် သတ်မှတ်နိုင်ပါတယ်။ 

#### Retrieving Intermediate Table Columns

လေ့လာခဲ့တဲ့အတိုင်း Many-to-many relationship ဟာ ကြားခံဆက်စပ် table တစ်ခုလိုအပ်ပါတယ်။ Eloquent ဟာ ကြားခံ table နဲ့ ဆက်သွယ်ဖို့ အထောက်အကူဖြစ်တဲ့ နည်းလမ်းတွေအများကြီးပေးပါတယ်။ ဥပမာ `User` object တစ်ခုမှာ သူနဲ့ ဆက်စပ်နေတဲ့ `Role` objects  တွေအများကြီးရှိတယ် ဆိုကြပါစို့၊ Relationship တွေသတ်မှတ်ပြီးတဲ့အခါမှာ ကြားခံ table ကို `pivot` ဆိုတဲ့ attribute ကိုသုံးပြီး ချိတ်ဆက်အသုံးပြုနိုင်ပါတယ်။

    $user = App\User::find(1);

    foreach ($user->roles as $role) {
        echo $role->pivot->created_at;
    }
ထုတ်ယူလိုက်တဲ့ `Role` model တစ်ခုချင်းစီဟာ `pivot` attribute ထဲမှာ အလိုအလျောက် နေရာယူသွားပြီဆိုတာကို သတိပြုပါ။ ဒီ attribute ထဲမှာ ကြားခံ table ကို ကိုယ်စားပြုတဲ့ model တစ်ခု ဝင်ရောက်သွားတဲ့အတွက် အခြား eloquent model တွေလိုပဲ သူ့ကိုအသုံးပြုနိုင်ပါပြီ။

`pivot` table ထဲမှာ model keys တွေသာပါဝင်မှာဖြစ်ပါတယ်။ အကယ်၍ pivot table ထဲမှာ extra attributes တွေထပ်ထည့်လိုလျှင် relationship သတ်မှတ်ချိန်မှာ ထည့်ပေးရပါမယ်။

    return $this->belongsToMany('App\Role')->withPivot('column1', 'column2');

အကယ်၍ pivot table မှာ `created_at` and `updated_at` အစရှိတဲ့ နေ့ရက်တန်ဖိုးတွေအလိုအလျောက်ထည့်စေချင်လျှင် `withTimestamps` ဆိုတဲ့ method ကို relationship စတင်သတ်မှတ်တဲ့အချိန်မှာ ထည့်ပေးရပါမယ်။

    return $this->belongsToMany('App\Role')->withTimestamps();

#### Customizing The `pivot` Attribute Name


As noted earlier, attributes from the intermediate table may be accessed on models using the `pivot` attribute. However, you are free to customize the name of this attribute to better reflect its purpose within your application.

For example, if your application contains users that may subscribe to podcasts, you probably have a many-to-many relationship between users and podcasts. If this is the case, you may wish to rename your intermediate table accessor to `subscription` instead of `pivot`. This can be done using the `as` method when defining the relationship:

    return $this->belongsToMany('App\Podcast')
                    ->as('subscription')
                    ->withTimestamps();

Once this is done, you may access the intermediate table data using the customized name:

    $users = User::with('podcasts')->get();

    foreach ($users->flatMap->podcasts as $podcast) {
        echo $podcast->subscription->created_at;
    }

#### Filtering Relationships Via Intermediate Table Columns

You can also filter the results returned by `belongsToMany` using the `wherePivot` and `wherePivotIn` methods when defining the relationship:

    return $this->belongsToMany('App\Role')->wherePivot('approved', 1);

    return $this->belongsToMany('App\Role')->wherePivotIn('priority', [1, 2]);

#### Defining Custom Intermediate Table Models

If you would like to define a custom model to represent the intermediate table of your relationship, you may call the `using` method when defining the relationship. Custom many-to-many pivot models should extend the `Illuminate\Database\Eloquent\Relations\Pivot` class while custom polymorphic many-to-many pivot models should extend the `Illuminate\Database\Eloquent\Relations\MorphPivot` class. For example, we may define a `Role` which uses a custom `UserRole` pivot model:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Role extends Model
    {
        /**
         * The users that belong to the role.
         */
        public function users()
        {
            return $this->belongsToMany('App\User')->using('App\UserRole');
        }
    }

When defining the `UserRole` model, we will extend the `Pivot` class:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Relations\Pivot;

    class UserRole extends Pivot
    {
        //
    }

Of course, you can combine `using` and `withPivot` in order to retrieve columns from the intermediate table. For example, you may retrieve the `created_by` and `updated_by` columns from the `UserRole` pivot table by passing the column names to the `withPivot` method:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Role extends Model
    {
        /**
         * The users that belong to the role.
         */
        public function users()
        {
            return $this->belongsToMany('App\User')
                            ->using('App\UserRole')
                            ->withPivot([
                                'created_by',
                                'updated_by'
                            ]);
        }
    }

<a name="has-many-through"></a>
### Has Many Through

The "has-many-through" relationship provides a convenient shortcut for accessing distant relations via an intermediate relation. For example, a `Country` model might have many `Post` models through an intermediate `User` model. In this example, you could easily gather all blog posts for a given country. Let's look at the tables required to define this relationship:

    countries
        id - integer
        name - string

    users
        id - integer
        country_id - integer
        name - string

    posts
        id - integer
        user_id - integer
        title - string

Though `posts` does not contain a `country_id` column, the `hasManyThrough` relation provides access to a country's posts via `$country->posts`. To perform this query, Eloquent inspects the `country_id` on the intermediate `users` table. After finding the matching user IDs, they are used to query the `posts` table.

Now that we have examined the table structure for the relationship, let's define it on the `Country` model:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Country extends Model
    {
        /**
         * Get all of the posts for the country.
         */
        public function posts()
        {
            return $this->hasManyThrough('App\Post', 'App\User');
        }
    }

The first argument passed to the `hasManyThrough` method is the name of the final model we wish to access, while the second argument is the name of the intermediate model.

Typical Eloquent foreign key conventions will be used when performing the relationship's queries. If you would like to customize the keys of the relationship, you may pass them as the third and fourth arguments to the `hasManyThrough` method. The third argument is the name of the foreign key on the intermediate model. The fourth argument is the name of the foreign key on the final model. The fifth argument is the local key, while the sixth argument is the local key of the intermediate model:

    class Country extends Model
    {
        public function posts()
        {
            return $this->hasManyThrough(
                'App\Post',
                'App\User',
                'country_id', // Foreign key on users table...
                'user_id', // Foreign key on posts table...
                'id', // Local key on countries table...
                'id' // Local key on users table...
            );
        }
    }

<a name="polymorphic-relationships"></a>
## Polymorphic Relationships

A polymorphic relationship allows the target model to belong to more than one type of model using a single association.

<a name="one-to-one-polymorphic-relations"></a>
### One To One (Polymorphic)

#### Table Structure

A one-to-one polymorphic relation is similar to a simple one-to-one relation; however, the target model can belong to more than one type of model on a single association. For example, a blog `Post` and a `User` may share a polymorphic relation to an `Image` model. Using a one-to-one polymorphic relation allows you to have a single list of unique images that are used for both blog posts and user accounts. First, let's examine the table structure:

    posts
        id - integer
        name - string

    users
        id - integer
        name - string

    images
        id - integer
        url - string
        imageable_id - integer
        imageable_type - string

Take note of the `imageable_id` and `imageable_type` columns on the `images` table. The `imageable_id` column will contain the ID value of the post or user, while the `imageable_type` column will contain the class name of the parent model. The `imageable_type` column is used by Eloquent to determine which "type" of parent model to return when accessing the `imageable` relation.

#### Model Structure

Next, let's examine the model definitions needed to build this relationship:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Image extends Model
    {
        /**
         * Get all of the owning imageable models.
         */
        public function imageable()
        {
            return $this->morphTo();
        }
    }

    class Post extends Model
    {
        /**
         * Get the post's image.
         */
        public function image()
        {
            return $this->morphOne('App\Image', 'imageable');
        }
    }

    class User extends Model
    {
        /**
         * Get the user's image.
         */
        public function image()
        {
            return $this->morphOne('App\Image', 'imageable');
        }
    }

#### Retrieving The Relationship

Once your database table and models are defined, you may access the relationships via your models. For example, to retrieve the image for a post, we can use the `image` dynamic property:

    $post = App\Post::find(1);

    $image = $post->image;

You may also retrieve the parent from the polymorphic model by accessing the name of the method that performs the call to `morphTo`. In our case, that is the `imageable` method on the `Image` model. So, we will access that method as a dynamic property:

    $image = App\Image::find(1);

    $imageable = $image->imageable;

The `imageable` relation on the `Image` model will return either a `Post` or `User` instance, depending on which type of model owns the image.

<a name="one-to-many-polymorphic-relations"></a>
### One To Many (Polymorphic)

#### Table Structure

A one-to-many polymorphic relation is similar to a simple one-to-many relation; however, the target model can belong to more than one type of model on a single association. For example, imagine users of your application can "comment" on both posts and videos. Using polymorphic relationships, you may use a single `comments` table for both of these scenarios. First, let's examine the table structure required to build this relationship:

    posts
        id - integer
        title - string
        body - text

    videos
        id - integer
        title - string
        url - string

    comments
        id - integer
        body - text
        commentable_id - integer
        commentable_type - string

#### Model Structure

Next, let's examine the model definitions needed to build this relationship:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Comment extends Model
    {
        /**
         * Get all of the owning commentable models.
         */
        public function commentable()
        {
            return $this->morphTo();
        }
    }

    class Post extends Model
    {
        /**
         * Get all of the post's comments.
         */
        public function comments()
        {
            return $this->morphMany('App\Comment', 'commentable');
        }
    }

    class Video extends Model
    {
        /**
         * Get all of the video's comments.
         */
        public function comments()
        {
            return $this->morphMany('App\Comment', 'commentable');
        }
    }

#### Retrieving The Relationship

Once your database table and models are defined, you may access the relationships via your models. For example, to access all of the comments for a post, we can use the `comments` dynamic property:

    $post = App\Post::find(1);

    foreach ($post->comments as $comment) {
        //
    }

You may also retrieve the owner of a polymorphic relation from the polymorphic model by accessing the name of the method that performs the call to `morphTo`. In our case, that is the `commentable` method on the `Comment` model. So, we will access that method as a dynamic property:

    $comment = App\Comment::find(1);

    $commentable = $comment->commentable;

The `commentable` relation on the `Comment` model will return either a `Post` or `Video` instance, depending on which type of model owns the comment.

<a name="many-to-many-polymorphic-relations"></a>
### Many To Many (Polymorphic)

#### Table Structure

Many-to-many polymorphic relations are slightly more complicated than `morphOne` and `morphMany` relationships. For example, a blog `Post` and `Video` model could share a polymorphic relation to a `Tag` model. Using a many-to-many polymorphic relation allows you to have a single list of unique tags that are shared across blog posts and videos. First, let's examine the table structure:

    posts
        id - integer
        name - string

    videos
        id - integer
        name - string

    tags
        id - integer
        name - string

    taggables
        tag_id - integer
        taggable_id - integer
        taggable_type - string

#### Model Structure

Next, we're ready to define the relationships on the model. The `Post` and `Video` models will both have a `tags` method that calls the `morphToMany` method on the base Eloquent class:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Post extends Model
    {
        /**
         * Get all of the tags for the post.
         */
        public function tags()
        {
            return $this->morphToMany('App\Tag', 'taggable');
        }
    }

#### Defining The Inverse Of The Relationship

Next, on the `Tag` model, you should define a method for each of its related models. So, for this example, we will define a `posts` method and a `videos` method:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Tag extends Model
    {
        /**
         * Get all of the posts that are assigned this tag.
         */
        public function posts()
        {
            return $this->morphedByMany('App\Post', 'taggable');
        }

        /**
         * Get all of the videos that are assigned this tag.
         */
        public function videos()
        {
            return $this->morphedByMany('App\Video', 'taggable');
        }
    }

#### Retrieving The Relationship

Once your database table and models are defined, you may access the relationships via your models. For example, to access all of the tags for a post, you can use the `tags` dynamic property:

    $post = App\Post::find(1);

    foreach ($post->tags as $tag) {
        //
    }

You may also retrieve the owner of a polymorphic relation from the polymorphic model by accessing the name of the method that performs the call to `morphedByMany`. In our case, that is the `posts` or `videos` methods on the `Tag` model. So, you will access those methods as dynamic properties:

    $tag = App\Tag::find(1);

    foreach ($tag->videos as $video) {
        //
    }

<a name="custom-polymorphic-types"></a>
### Custom Polymorphic Types

By default, Laravel will use the fully qualified class name to store the type of the related model. For instance, given the one-to-many example above where a `Comment` may belong to a `Post` or a `Video`, the default `commentable_type` would be either `App\Post` or `App\Video`, respectively. However, you may wish to decouple your database from your application's internal structure. In that case, you may define a "morph map" to instruct Eloquent to use a custom name for each model instead of the class name:

    use Illuminate\Database\Eloquent\Relations\Relation;

    Relation::morphMap([
        'posts' => 'App\Post',
        'videos' => 'App\Video',
    ]);

You may register the `morphMap` in the `boot` function of your `AppServiceProvider` or create a separate service provider if you wish.

<a name="querying-relations"></a>
## Querying Relations

Since all types of Eloquent relationships are defined via methods, you may call those methods to obtain an instance of the relationship without actually executing the relationship queries. In addition, all types of Eloquent relationships also serve as [query builders](/docs/{{version}}/queries), allowing you to continue to chain constraints onto the relationship query before finally executing the SQL against your database.

For example, imagine a blog system in which a `User` model has many associated `Post` models:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Get all of the posts for the user.
         */
        public function posts()
        {
            return $this->hasMany('App\Post');
        }
    }

You may query the `posts` relationship and add additional constraints to the relationship like so:

    $user = App\User::find(1);

    $user->posts()->where('active', 1)->get();

You are able to use any of the [query builder](/docs/{{version}}/queries) methods on the relationship, so be sure to explore the query builder documentation to learn about all of the methods that are available to you.

<a name="relationship-methods-vs-dynamic-properties"></a>
### Relationship Methods Vs. Dynamic Properties

If you do not need to add additional constraints to an Eloquent relationship query, you may access the relationship as if it were a property. For example, continuing to use our `User` and `Post` example models, we may access all of a user's posts like so:

    $user = App\User::find(1);

    foreach ($user->posts as $post) {
        //
    }

Dynamic properties are "lazy loading", meaning they will only load their relationship data when you actually access them. Because of this, developers often use [eager loading](#eager-loading) to pre-load relationships they know will be accessed after loading the model. Eager loading provides a significant reduction in SQL queries that must be executed to load a model's relations.

<a name="querying-relationship-existence"></a>
### Querying Relationship Existence

When accessing the records for a model, you may wish to limit your results based on the existence of a relationship. For example, imagine you want to retrieve all blog posts that have at least one comment. To do so, you may pass the name of the relationship to the `has` and `orHas` methods:

    // Retrieve all posts that have at least one comment...
    $posts = App\Post::has('comments')->get();

You may also specify an operator and count to further customize the query:

    // Retrieve all posts that have three or more comments...
    $posts = App\Post::has('comments', '>=', 3)->get();

Nested `has` statements may also be constructed using "dot" notation. For example, you may retrieve all posts that have at least one comment and vote:

    // Retrieve all posts that have at least one comment with votes...
    $posts = App\Post::has('comments.votes')->get();

If you need even more power, you may use the `whereHas` and `orWhereHas` methods to put "where" conditions on your `has` queries. These methods allow you to add customized constraints to a relationship constraint, such as checking the content of a comment:

    // Retrieve all posts with at least one comment containing words like foo%
    $posts = App\Post::whereHas('comments', function ($query) {
        $query->where('content', 'like', 'foo%');
    })->get();

<a name="querying-relationship-absence"></a>
### Querying Relationship Absence

When accessing the records for a model, you may wish to limit your results based on the absence of a relationship. For example, imagine you want to retrieve all blog posts that **don't** have any comments. To do so, you may pass the name of the relationship to the `doesntHave` and `orDoesntHave` methods:

    $posts = App\Post::doesntHave('comments')->get();

If you need even more power, you may use the `whereDoesntHave` and `orWhereDoesntHave` methods to put "where" conditions on your `doesntHave` queries. These methods allows you to add customized constraints to a relationship constraint, such as checking the content of a comment:

    $posts = App\Post::whereDoesntHave('comments', function ($query) {
        $query->where('content', 'like', 'foo%');
    })->get();

You may use "dot" notation to execute a query against a nested relationship. For example, the following query will retrieve all posts with comments from authors that are not banned:

    $posts = App\Post::whereDoesntHave('comments.author', function ($query) {
        $query->where('banned', 1);
    })->get();

<a name="counting-related-models"></a>
### Counting Related Models

If you want to count the number of results from a relationship without actually loading them you may use the `withCount` method, which will place a `{relation}_count` column on your resulting models. For example:

    $posts = App\Post::withCount('comments')->get();

    foreach ($posts as $post) {
        echo $post->comments_count;
    }

You may add the "counts" for multiple relations as well as add constraints to the queries:

    $posts = App\Post::withCount(['votes', 'comments' => function ($query) {
        $query->where('content', 'like', 'foo%');
    }])->get();

    echo $posts[0]->votes_count;
    echo $posts[0]->comments_count;

You may also alias the relationship count result, allowing multiple counts on the same relationship:

    $posts = App\Post::withCount([
        'comments',
        'comments as pending_comments_count' => function ($query) {
            $query->where('approved', false);
        }
    ])->get();

    echo $posts[0]->comments_count;

    echo $posts[0]->pending_comments_count;

If you're combining `withCount` with a `select` statement, ensure that you call `withCount` after the `select` method:

    $query = App\Post::select(['title', 'body'])->withCount('comments');

    echo $posts[0]->title;
    echo $posts[0]->body;
    echo $posts[0]->comments_count;

<a name="eager-loading"></a>
## Eager Loading

When accessing Eloquent relationships as properties, the relationship data is "lazy loaded". This means the relationship data is not actually loaded until you first access the property. However, Eloquent can "eager load" relationships at the time you query the parent model. Eager loading alleviates the N + 1 query problem. To illustrate the N + 1 query problem, consider a `Book` model that is related to `Author`:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Book extends Model
    {
        /**
         * Get the author that wrote the book.
         */
        public function author()
        {
            return $this->belongsTo('App\Author');
        }
    }

Now, let's retrieve all books and their authors:

    $books = App\Book::all();

    foreach ($books as $book) {
        echo $book->author->name;
    }

This loop will execute 1 query to retrieve all of the books on the table, then another query for each book to retrieve the author. So, if we have 25 books, this loop would run 26 queries: 1 for the original book, and 25 additional queries to retrieve the author of each book.

Thankfully, we can use eager loading to reduce this operation to just 2 queries. When querying, you may specify which relationships should be eager loaded using the `with` method:

    $books = App\Book::with('author')->get();

    foreach ($books as $book) {
        echo $book->author->name;
    }

For this operation, only two queries will be executed:

    select * from books

    select * from authors where id in (1, 2, 3, 4, 5, ...)

#### Eager Loading Multiple Relationships

Sometimes you may need to eager load several different relationships in a single operation. To do so, just pass additional arguments to the `with` method:

    $books = App\Book::with(['author', 'publisher'])->get();

#### Nested Eager Loading

To eager load nested relationships, you may use "dot" syntax. For example, let's eager load all of the book's authors and all of the author's personal contacts in one Eloquent statement:

    $books = App\Book::with('author.contacts')->get();

#### Eager Loading Specific Columns

You may not always need every column from the relationships you are retrieving. For this reason, Eloquent allows you to specify which columns of the relationship you would like to retrieve:

    $users = App\Book::with('author:id,name')->get();

> {note} When using this feature, you should always include the `id` column in the list of columns you wish to retrieve.

<a name="constraining-eager-loads"></a>
### Constraining Eager Loads

Sometimes you may wish to eager load a relationship, but also specify additional query constraints for the eager loading query. Here's an example:

    $users = App\User::with(['posts' => function ($query) {
        $query->where('title', 'like', '%first%');
    }])->get();

In this example, Eloquent will only eager load posts where the post's `title` column contains the word `first`. Of course, you may call other [query builder](/docs/{{version}}/queries) methods to further customize the eager loading operation:

    $users = App\User::with(['posts' => function ($query) {
        $query->orderBy('created_at', 'desc');
    }])->get();

<a name="lazy-eager-loading"></a>
### Lazy Eager Loading

Sometimes you may need to eager load a relationship after the parent model has already been retrieved. For example, this may be useful if you need to dynamically decide whether to load related models:

    $books = App\Book::all();

    if ($someCondition) {
        $books->load('author', 'publisher');
    }

If you need to set additional query constraints on the eager loading query, you may pass an array keyed by the relationships you wish to load. The array values should be `Closure` instances which receive the query instance:

    $books->load(['author' => function ($query) {
        $query->orderBy('published_date', 'asc');
    }]);

To load a relationship only when it has not already been loaded, use the `loadMissing` method:

    public function format(Book $book)
    {
        $book->loadMissing('author');

        return [
            'name' => $book->name,
            'author' => $book->author->name
        ];
    }

<a name="inserting-and-updating-related-models"></a>
## Inserting & Updating Related Models

<a name="the-save-method"></a>
### The Save Method

Eloquent provides convenient methods for adding new models to relationships. For example, perhaps you need to insert a new `Comment` for a `Post` model. Instead of manually setting the `post_id` attribute on the `Comment`, you may insert the `Comment` directly from the relationship's `save` method:

    $comment = new App\Comment(['message' => 'A new comment.']);

    $post = App\Post::find(1);

    $post->comments()->save($comment);

Notice that we did not access the `comments` relationship as a dynamic property. Instead, we called the `comments` method to obtain an instance of the relationship. The `save` method will automatically add the appropriate `post_id` value to the new `Comment` model.

If you need to save multiple related models, you may use the `saveMany` method:

    $post = App\Post::find(1);

    $post->comments()->saveMany([
        new App\Comment(['message' => 'A new comment.']),
        new App\Comment(['message' => 'Another comment.']),
    ]);

<a name="the-push-method"></a>
#### Recursively Saving Models & Relationships

If you would like to `save` your model and all of its associated relationships, you may use the `push` method:

    $post = App\Post::find(1);

    $post->comments[0]->message = 'Message';
    $post->comments[0]->author->name = 'Author Name';

    $post->push();

<a name="the-create-method"></a>
### The Create Method

In addition to the `save` and `saveMany` methods, you may also use the `create` method, which accepts an array of attributes, creates a model, and inserts it into the database. Again, the difference between `save` and `create` is that `save` accepts a full Eloquent model instance while `create` accepts a plain PHP `array`:

    $post = App\Post::find(1);

    $comment = $post->comments()->create([
        'message' => 'A new comment.',
    ]);

> {tip} Before using the `create` method, be sure to review the documentation on attribute [mass assignment](/docs/{{version}}/eloquent#mass-assignment).

You may use the `createMany` method to create multiple related models:

    $post = App\Post::find(1);

    $post->comments()->createMany([
        [
            'message' => 'A new comment.',
        ],
        [
            'message' => 'Another new comment.',
        ],
    ]);

You may also use the `findOrNew`, `firstOrNew`, `firstOrCreate` and `updateOrCreate` methods to [create and update models on relationships](https://laravel.com/docs/{{version}}/eloquent#other-creation-methods).

<a name="updating-belongs-to-relationships"></a>
### Belongs To Relationships

When updating a `belongsTo` relationship, you may use the `associate` method. This method will set the foreign key on the child model:

    $account = App\Account::find(10);

    $user->account()->associate($account);

    $user->save();

When removing a `belongsTo` relationship, you may use the `dissociate` method. This method will set the relationship's foreign key to `null`:

    $user->account()->dissociate();

    $user->save();

<a name="default-models"></a>
#### Default Models

The `belongsTo` relationship allows you to define a default model that will be returned if the given relationship is `null`. This pattern is often referred to as the [Null Object pattern](https://en.wikipedia.org/wiki/Null_Object_pattern) and can help remove conditional checks in your code. In the following example, the `user` relation will return an empty `App\User` model if no `user` is attached to the post:

    /**
     * Get the author of the post.
     */
    public function user()
    {
        return $this->belongsTo('App\User')->withDefault();
    }

To populate the default model with attributes, you may pass an array or Closure to the `withDefault` method:

    /**
     * Get the author of the post.
     */
    public function user()
    {
        return $this->belongsTo('App\User')->withDefault([
            'name' => 'Guest Author',
        ]);
    }

    /**
     * Get the author of the post.
     */
    public function user()
    {
        return $this->belongsTo('App\User')->withDefault(function ($user) {
            $user->name = 'Guest Author';
        });
    }

<a name="updating-many-to-many-relationships"></a>
### Many To Many Relationships

#### Attaching / Detaching

Eloquent also provides a few additional helper methods to make working with related models more convenient. For example, let's imagine a user can have many roles and a role can have many users. To attach a role to a user by inserting a record in the intermediate table that joins the models, use the `attach` method:

    $user = App\User::find(1);

    $user->roles()->attach($roleId);

When attaching a relationship to a model, you may also pass an array of additional data to be inserted into the intermediate table:

    $user->roles()->attach($roleId, ['expires' => $expires]);

Of course, sometimes it may be necessary to remove a role from a user. To remove a many-to-many relationship record, use the `detach` method. The `detach` method will remove the appropriate record out of the intermediate table; however, both models will remain in the database:

    // Detach a single role from the user...
    $user->roles()->detach($roleId);

    // Detach all roles from the user...
    $user->roles()->detach();

For convenience, `attach` and `detach` also accept arrays of IDs as input:

    $user = App\User::find(1);

    $user->roles()->detach([1, 2, 3]);

    $user->roles()->attach([
        1 => ['expires' => $expires],
        2 => ['expires' => $expires]
    ]);

#### Syncing Associations

You may also use the `sync` method to construct many-to-many associations. The `sync` method accepts an array of IDs to place on the intermediate table. Any IDs that are not in the given array will be removed from the intermediate table. So, after this operation is complete, only the IDs in the given array will exist in the intermediate table:

    $user->roles()->sync([1, 2, 3]);

You may also pass additional intermediate table values with the IDs:

    $user->roles()->sync([1 => ['expires' => true], 2, 3]);

If you do not want to detach existing IDs, you may use the `syncWithoutDetaching` method:

    $user->roles()->syncWithoutDetaching([1, 2, 3]);

#### Toggling Associations

The many-to-many relationship also provides a `toggle` method which "toggles" the attachment status of the given IDs. If the given ID is currently attached, it will be detached. Likewise, if it is currently detached, it will be attached:

    $user->roles()->toggle([1, 2, 3]);

#### Saving Additional Data On A Pivot Table

When working with a many-to-many relationship, the `save` method accepts an array of additional intermediate table attributes as its second argument:

    App\User::find(1)->roles()->save($role, ['expires' => $expires]);

#### Updating A Record On A Pivot Table

If you need to update an existing row in your pivot table, you may use `updateExistingPivot` method. This method accepts the pivot record foreign key and an array of attributes to update:

    $user = App\User::find(1);

    $user->roles()->updateExistingPivot($roleId, $attributes);

<a name="touching-parent-timestamps"></a>
## Touching Parent Timestamps

When a model `belongsTo` or `belongsToMany` another model, such as a `Comment` which belongs to a `Post`, it is sometimes helpful to update the parent's timestamp when the child model is updated. For example, when a `Comment` model is updated, you may want to automatically "touch" the `updated_at` timestamp of the owning `Post`. Eloquent makes it easy. Just add a `touches` property containing the names of the relationships to the child model:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Comment extends Model
    {
        /**
         * All of the relationships to be touched.
         *
         * @var array
         */
        protected $touches = ['post'];

        /**
         * Get the post that the comment belongs to.
         */
        public function post()
        {
            return $this->belongsTo('App\Post');
        }
    }

Now, when you update a `Comment`, the owning `Post` will have its `updated_at` column updated as well, making it more convenient to know when to invalidate a cache of the `Post` model:

    $comment = App\Comment::find(1);

    $comment->text = 'Edit to this comment!';

    $comment->save();
