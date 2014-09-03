# অবজারভার প্যাটার্ন  

মুলত অবজেক্ট অরিয়েন্টেড প্রোগ্রামিং হচ্ছে  অবজেক্টের এর বিভিন্ন অবস্থা এবং এদের মাঝে বিভিন্ন রকমের ইন্টারেকশন এর সমষ্টি । একটি অবজেক্ট কি অবস্থায় আছে , এই অবজেক্টের সাথে অন্য অবজেক্টের রিলেশন কি এরা কিভাবে একে অপরের সাথে যোগাযোগ করে এগুলোই হচ্ছে অবজেক্ট অরিয়েন্টেড প্রোগ্রামিং এর বিষয়বস্তু । একজন অবজেক্ট অরিয়েন্টেড  প্রোগ্রামারকে এই ব্যাপারগুলো কোডের মাধ্যমে দক্ষতার সাথে হ্যান্ডেল করতে হয় ।

অনেক সময় দেখা যায় আমাদের এপ্লিকেশনে একটি অবজেক্টের কোনও প্রপার্টি চেঞ্জ হলে আমাদের কিছু কাজ করতে হয় অথবা অন্য কোনও অবজেক্টকে নোটিফিকেশন পাঠাতে হয় ।

নোটিফিকেশন পাবার পর ঐ অবজেক্টগুলোকে কিছু কাজ করতে হয় । যেমন বিভিন্ন মোবাইল অপারেটররা তাদের প্যাকেজ চেঞ্জ করলে অথবা নতুন অফার বাজারে ছাড়লে তা আমাদের এস এম এস এর মাধ্যমে জানিয়ে দেয় ।

অবজারভার প্যাটার্নের ক্ষেত্রে আমাদের দুটি জিনিস মনে রাখা জরুরী একটি হচ্ছে Subject এবং অন্যটি হচ্ছে Observer . উপরের উদাহরণ এর ক্ষেত্রে আমরা যারা বিভিন্ন অপারেটরের সিম ব্যবহার করি তারা হচ্ছি Observer  এবং মোবাইল অপারেটর হচ্ছে Subject . Subject এর একটি প্রপার্টি যেমন প্যাকেজ চেঞ্জ হলেই আমরা যারা Observer তারা নোটিফিকেশন পাই । এক্ষেত্রে আমরা যারা Observer তারা হচ্ছি Subject এর উপর ডিপেন্ডেন্ট ।

GOF এর সংজ্ঞা অনুযায়ী Observer Pattern হচ্ছেঃ “Define a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.”

অর্থাৎ Subject এবং Observer এর মধ্য এমন একটি রিলেশন তৈরি করা যাতে করে যখনই Subject  চেঞ্জ হবে তখনই Automatically Observer গুলো নোটিফিকেশন পেয়ে যাবে ।

খুব দূরে না গিয়ে চলুন এই ব্লগ দিয়েই একটা উদাহরণ দেয়া যাক ।

প্রত্যকটি ব্লগেই একটা Subscribe অপশন থাকে কেউ যদি এই ব্লগ থেকে নতুন পোষ্ট পেতে চায় তাকে শুধুমাত্র ইমেইলের মাধ্যমে Subscribe করতে হবে । ব্লগে নতুন কোনও পোষ্ট এলে সেই পোষ্টের লিংক অটোমেটিক্যালি তার সেই ইমেইলে চলে যাবে । ব্লগে ঢুকে তাকে আর বার বার চেক করতে হবে না যে নতুন কোনও পোষ্ট এসেছে কিনা । ওয়ার্ডপ্রেসে এই মডিউলটা  বানিয়েই দেয়া আছে ।এখন আমরা যদি ওয়ার্ডপ্রেস এর এই মডিউলটার মত একটি মডিউল  নিজেরা বানাতে চাই , তাহলে কিভাবে বানাবো । ??

চলুন দেখে আসি ।

ব্লগে যেহেতু অনেক Subscriber থাকতে পারে এবং তাদের সবারই একটা কমন প্রপার্টি থাকবে নাম । এবং সবাই কিছু মেসেজ পাবে এর Subject এর কাছ থেকে । ধরা যাক GetNotification() মেথডের  মাধ্যমে সে কিছু মেসেজ পাবে। তাহলে এই জিনিসগুলো আমরা একটা ইন্টারফেস এ রেখে দিই ।

তাহলে আমাদের ইন্টারফেসটা দাঁড়াবে এরকমঃ

```java
public interface ISubscriber
{
  void GetNotification(String  postTitle);
  String Name { get; set; }
 }
```

এখন পোষ্টের কমেন্টে কেউ যদি স্প্যাম অথবা কোনও বাজে  কমেন্ট করেন এগুলো দেখার জন্য একজন Moderator থাকবে । এই Moderator ও কিন্তু একজন Subscriber. সো আমাদের Moderator ISubscriber কে এক্সটেন্ড করবে এইভাবে:

```java
public class Moderator : ISubscriber
    {
        public String Name { get; set; }
        public Moderator(String name)
        {
            Name = name;
        }
        public void GetNotification(String postTitle)
        {
            Console.WriteLine("Hello Mr:{0} A new post named {1} has been posted in the blognyou need to moderate the comments", Name, postTitle);
        }
     }
```

এভাবে ব্লগের সাধারন একজন User  বানাই যে কিনা ISubscriber কে এক্সটেন্ড করবেঃ

```java
public class User : ISubscriber
    {
        public String Name { get; set; }
        public User(String name)
        {
            Name = name;
        }
        public void GetNotification(String postTitle)
        {
            Console.WriteLine("Hey {0} Checkout the new post named {1}", Name, postTitle);
        }
    }
```

এখন আমাদের Blog এর Subscriber দের নোটিফিকেশন পাঠানোর কোড টুকু লিখে ফেলিঃ

```java
public class Blog
    {
        private String _postTitle;
        public String PostTitle
        {
            get
            {
                return _postTitle;
            }
            set
            {
                if (_postTitle != value)
                {
                    _postTitle = value;
                    NotifySubscribers();
                }
            }
        }
        private void NotifySubscribers()
        {
            ISubscriber observer = new User("Forhad");
            observer.GetNotification(_postTitle);
            observer = new Moderator("Modu");
            observer.GetNotification(_postTitle);
        }
    }
```

এখানে আমরা আমাদের ব্লগের পোষ্টের একটি টাইটেল এর জন্য Getter এবং Setter বানিয়েছি । Setter এর মধ্য আমরা চেক করেছি টাইটেলটা আগের কোনও টাইটেলের সাথে মিলে যায় কিনা । মিলে না গেলে আমরা আমাদের টাইটেলে নতুন টাইটেলটি  সেট করেছি এবং নতুন পোষ্ট টাইটেল সেট করার সাথে সাথে NotifySubscribers()  মেথডটি কল করেছি । NotifySubscribers() এর মধ্য আমরা ISubscriber এখানে দিয়ে আমাদের ব্লগের জন্য User এবং Moderator বানিয়েছি এবং উভয়ের GetNotification() মেথডের মধ্য দিয়ে পোষ্ট এর টাইটেল টি সেন্ড করে দিয়েছি ।

এখন আমাদের ClientCode টি দেখা যাক :

```java
public class Program
    {
        public static void Main(string[] args)
        {
            Blog blog = new Blog ();
            blog.PostTitle = "ObserverPattern";
            Console.ReadLine();
        }
    }
```

যাক আমরা যখনই ব্লগ পোষ্টের টাইটেল সেট করলাম তখনই সে Blog এর প্রাইভেট মেথড NotifySubscribers() কল করেছে এবং এই মেথডের মধ্য সে Blog এর সব Subscriber এর অবজেক্ট বানিয়ে তাদের নতুন পোষ্টের টাইটেল পাঠিয়ে দিয়েছে ।

ব্যস হয়ে গেলো আমাদের ব্লগের মডিউল বানানোর কাজ । এরই মধ্য আমি এই মডিউলটি একটি CMS বানায় এরকম একটি কোম্পানীর কাছে বিক্রি করে দিলাম এবং সোর্সকোড এর বদলে আমি একে একটি ClassLibrary (DLL) বানিয়ে দিয়ে দেয়ার আগে আমি DLL এর ভেতর সোর্স কোড গুলো Obfuscated Code সোর্স মানে যেনো Decompile/Reverse Engineering করেও সোর্স কোড উদ্ধার করতে না পারে এরকম করে দিলাম ।

ঐ CMS কোম্পানী নতুন একধরনের Subscriber বানাতে চাইলো ধরা যাক Administrator । এখন কিভাবে সে নতুন টাইপের Subscriber বানাবে । আমি যে DLL তাকে দিয়ে দিয়েছিলাম সে ঐটা দিয়ে নতুন টাইপের Subscriber বানাতে পারবেনা । কেননা নতুন Subscriber বানাতে হলে তাকে Blog class টাতে চেঞ্জ করতে হবে । Administrator নামে একটি class বানিয়ে তাকে Blog class এর NotifySubscribers()মেথডের মধ্য এর অবজেক্ট বানাতে হবে তারপর GetNotification() কল করতে হবে । এইভাবে তাকে নতুন টাইপের Subscriber বানাতে হবে । কিন্তু আমার DLL এ সে শুধু Blog class এবং PostTitle এক্সেস করতে পারবে তা ছাড়া আর কিছু পারবে না । তার মানে আমার দেয়া DLL তার কোনও কাজে আসবে না ।

আর আমি যদি DLL না বানিয়েও শুধু মাত্র সোর্স কোড সহ দিয়ে দিতাম তাহলেও এটি একটি বাজে ডিজাইন হতো । কেননা এটি SOLID প্রিন্সিপালের OCP (Open Closed Principle) এর ভায়োলেশন করতো ।

OCP আমাদের বলে যে একটি ক্লাস/মেথড আমরা এমন ভাবে লিখব যাতে করে আমরা চাইলেই এক্সটেন্ড করতে পারবো কিন্তু মোডিফিকেশন করতে পারবো না ।

প্রত্যেকবার আমাদের Blog class এর NotifySubscribers() মোডিফিকেশন করতে হবে নতুন টাইপের Subscriber যোগ করার জন্য ।

খুব স্বাভাবিকভাবেই কোম্পানিটি আমাকে আমার ClassLibrary এর ডিজাইন চেঞ্জ করে দিতে বললো । আমি কয়েকদিন সময় চেয়ে নিলাম  ডিজাইন ঠিক করে দেয়ার জন্য।

তাহলে আসুন আমরা সবাই মিলে ডিজাইনটা চেঞ্জ করার চেষ্ঠা করে দেখি পারি কি না ।

প্রথমে আমরা NotifySubscribers() মেথড এর দিকে নজর দিই । আমরা দেখতে পাবো অনেক গুলো  হার্ড কোডেড অবজেক্ট বানানো আছে । আমরা চাচ্ছি আমরা ClientCode থেকে অবজেক্ট বানাবো কোনও মেথডের মাধ্যমে । তাহলে  যে কোম্পানি Blog এর অবজেক্ট বানাবে এবং আমাদের বানানো মেথডের মাধ্যমে যত ইচ্ছা তত রকমের তত প্রকারের Subscriber যোগ করতে পারবে তাদের মডিউলের জন্য । মনে করি এই নতুন মেথডের নাম Subscribe() এবং এই মেথড প্যারামিটার হিসেবে ISubscriber নেয় ।

তাহলে আমাদের NotifySubscribers() মেথডের মধ্য আর কোনোও হার্ডকোডেড অবজেক্ট থাকলো না । কিন্তু এই খালি মেথডের তো কিছু একটা করা লাগবে । আমরা যা করতে পারি তা হচ্ছে Blog এর মধ্যে আমাদের যতগুলো Subscriber আছে সবাইকে  নতুন পোষ্টের খবরটা পাঠিয়ে দিতে পারি ।  তাহলে আমাদের এই Blog class এ সকল Subscriber এর একটা লিস্ট থাকবে । অর্থাৎ আমাদের ISubscriber টাইপের একটা লিস্ট রাখলেই হচ্ছে এই class এ কেননা সকল টাইপের Subscriber ই ISubscriber কে ইমপ্লিমেন্ট করে ।

Subscribe() মেথডের মধ্য আমরা যে ISubscriber কে পাঠিয়েছিলাম ঐ ISubscriber কে আমরা আমাদের লিস্টে এড করে নিবো । এভাবে ClientCode থেকে আমরা যত খুশি Subscriber এড করে নিতে পারবো ।

তাহলে আমরা NotifySubscribers() এর মধ্য একটা লুপ চালিয়ে সকল ISubscriber এর GetNotification() কল করলেই হয়ে যাচ্ছে । GetNotification() এর প্যারামিটার হিসেবে Blog class এর _postTitle তো আছেই । তাহলে আমাদের নতুন ডিজাইন Blog class টা আমরা দেখে ফেলিঃ

```java
public class Blog
    {
        private String _postTitle;
        private readonly List<ISubscriber> _subscribers;
 
        public Blog()
        {
            _subscribers = new List<ISubscriber>();
        }
        public String PostTitle
        {
            get
            {
                return _postTitle;
            }
            set
            {
                if (_postTitle != value)
                {
                    _postTitle = value;
                    NotifySubscribers(_subscribers);
                }
            }
        }
 
        public void Subscribe(ISubscriber subscriber)
        {
            _subscribers.Add(subscriber);
        }
 
        private void NotifySubscribers(List<ISubscriber> subscribers)
        {
            foreach (ISubscriber subscriber in subscribers)
            {
                subscriber.GetNotification(_postTitle);
            }
        }
    }
```

তাহলে আমাদের ClientCode কেমন হলো একটু দেখে আসিঃ

```java
public class Program
    {
        public static void Main(string[] args)
        {
            Blog blog = new Blog();
 
            blog.Subscribe(new Moderator("Modu"));
 
            blog.Subscribe(new User("Forhad"));
 
            blog.PostTitle = "Observer Pattern";
 
            Console.ReadLine();
        }
    }
```

এখন যদি আমি আমার ClassLibrary টা ঐ কোম্পানি কে দেই । তারা নতুন টাইপের Subscriber যোগ করার জন্য এর class বানাবে যেটি ISubscriber কে ইমপ্লিমেন্ট করে তারপর ClientCode এ Blog এর একটি অবজেক্ট বানাবে এবং এর Subscribe মেথডের ভেতর দিয়ে নতুন টাইপের Subscriber এর অবজেক্ট দিয়ে দিবে তারপর পোষ্ট এর টাইটেল সেট করলেই Blog এর Subscriber সবাই পেয়ে যাবে । এভাবে যত খুশি তত Subscriber এড করা যাবে । যদি কাউকে আমরা Blog থেকে ব্যান করতে চাই তখন আমরা Blog এ আরেকটি মেথড লিখব UnSubscribe এ নামে যেটিও কিনা Subscribe মেথডের মত একটি ISubscriber এর একটি রেফারেন্স নিবে এবং এর মধ্যে আমরা আমাদের লিস্ট থেকে এটিকে রিমুভ করবো । তাহলে এই Subscriber আর নতুন পোষ্টের মেসেজ পাবে না ।

আবার আমরা সব সময় Blog class টা ইউজ করছি । কোম্পানী যদি নতুন কোনও class বানিয়ে কাজ গুলো করতে চায় তাহলে তারা পারবেনা । কারন সকল মেথড লজিক Blog class এ । আমরা ইচ্ছা করলে এই মেথডগুলো একটি ইন্টারফেস এ নিয়ে যেতে পারি । যাতে করে যে কেউ ইচ্ছা করলে এই ইন্টারফেস কে ইমপ্লিমেন্ট করে কাজ করতে পারে ।

তাহলে চলুন লিখে ফেলি আমাদের  Unsubscribe মেথডঃ

```java
public class Blog
    {
        private String _postTitle;
        private readonly List<ISubscriber> _subscribers;
 
        public Blog()
        {
            _subscribers = new List<ISubscriber>();
        }
        public String PostTitle
        {
            get
            {
                return _postTitle;
            }
            set
            {
                if (_postTitle != value)
                {
                    _postTitle = value;
                    NotifySubscribers(_subscribers);
                }
            }
        }
 
        public void Subscribe(ISubscriber subscriber)
        {
            _subscribers.Add(subscriber);
        }
 
        public void UnSubscribe(ISubscriber subscriber)
        {
            _subscribers.Remove(subscriber);
        }
 
        private void NotifySubscribers(List<ISubscriber> subscribers)
        {
            foreach (ISubscriber subscriber in subscribers)
            {
                subscriber.GetNotification(_postTitle);
            }
        }
    }
```

তাহলে  আমাদের ClientCode হবে এরকমঃ

```java
public class Program
    {
        public static void Main(string[] args)
        {
            Blog blog = new Blog();
 
            ISubscriber user = new User("DemoUser");
            ISubscriber moderator = new User("Modu");
            blog.Subscribe(moderator);
            blog.Subscribe(user);
            blog.PostTitle = "Observer Pattern";
 
            blog.UnSubscribe(moderator);//we unsubscribed themoderator
            blog.PostTitle = "Singleton";
            Console.ReadLine();
        }
    }
```

এখন আমরা Blog class থেকে এর মেথড গুলো আলাদা একটা ইন্টারফেস এ নিয়ে যাই ।

এর নাম দিই IObservable:

```java
public interface IObservable
    {
        void Subscribe(ISubscriber subscriber);
        void UnSubscribe(ISubscriber subscriber);
        void NotifySubscribers(List<ISubscriber> subscribers);
    }
```    

এখন আমরা আমাদের ফাইনাল কোড দেখে ফেলিঃ

```java
public class Blog : IObservable
    {
        private String _postTitle;
        private readonly List<ISubscriber> _subscribers;
        public Blog()
        {
            _subscribers = new List<ISubscriber>();
        }
        public String PostTitle
        {
            get
            {
                return _postTitle;
            }
            set
            {
                if (_postTitle != value)
                {
                    _postTitle = value;
                    NotifySubscribers(_subscribers);
                }
            }
        }
 
        public void Subscribe(ISubscriber subscriber)
        {
            _subscribers.Add(subscriber);
        }
 
        public void UnSubscribe(ISubscriber subscriber)
        {
            _subscribers.Remove(subscriber);
        }
 
        public void NotifySubscribers(List<ISubscriber> subscribers)
        {
            foreach (ISubscriber subscriber in subscribers)
            {
                subscriber.GetNotification(_postTitle);
            }
        }
 
    }

```

এবং আমাদের ClientCode আগের মতই থাকবে :)

এখন  আমি আমার DLL কোম্পানীর কাছে দিয়ে দিলে  এবং তাদের শুধুমাত্র Interface টার নাম বলে দিলেই ,তারা ইন্টারফেসটাকে ইমপ্লিমেন্ট করে যে কোনও class বানাবে , এর মেথডগুলো কমপ্লিট করবে  ClientCode এ যত খুশি Subscriber বানিয়ে Blog এর টাইটেল সেট করলেই সকল প্রকার সাবস্ক্রাইবার ব্লগের পোষ্ট পেয়ে যাবে ।

এটাই হচ্ছে আমাদের Observer Design Pattern.

পুনশ্চঃ এখানে দেখা যাচ্ছে যে আমাদের Subject সব সময় (এক্ষেত্রে Blog) কোনও ইভেন্ট আপডেট করছে (এক্ষেত্রে PostTitle) অর্থাৎ ইভেন্ট Push করছে । আবার এমন সময় দরকার হতে পারে যে Observer রাই এসে কোনও নতুন ইভেন্ট থেকে থাকলে সেটা Pull করে নিয়ে যাবে । এই Push Pull এর ঝামেলা গুলো Java এবং .Net এর API দিয়েই সমাধান করা যায় । আপনারা একটু গুগল করলেই Java এবং .Net এর বানিয়ে দেয়া class এবং interface গুলো খুজে পাবেন ।

Java এবং .Net এ আপনারা অনেক উদাহরন দেখতে পাবেন Observer Pattern এর । যেমন সকল প্রকার ইভেন্ট ম্যানেজমেন্ট গুলো (যেমন বাটন একশন ইভেন্ট, মাউস ক্লিক ইভেন্ট) এগুলো সবই এই প্যাটার্নে করা ।

আবার বর্তমান সময়ের সফটওয়্যার ডেভেলেপমেন্ট ক্রেজ MVC Architectural Pattern এর Model এবং View হচ্ছে যথাক্রমে Observable এবং Observer যা কিনা একটি Observer Pattern.

এই প্যাটার্নকে আপনি “চুপি চুপি বল কেউ জেনে যাবে “ এই ধরনের কথা বলতে পারবেন না । চুপি চুপি হোক আস্তে আস্তে হোক যেভাবেই বলুন না কেনো আসলে সবাই জেনে যাবে আসল ঘটনা :P