# Iterator ডিজাইন প্যাটার্ন  

মনে করুন একটি সফটওয়্যার ফার্ম ফেসবুক এবং টুইটারের যতগুলো ইউজার আছে তাদের সম্পূর্ণ তথ্যগুলো একটি কমন ডেটাবেইজ এ রাখতে চায় । (যদিও বাস্তবে ফেসবুক এবং টুইটার কখনও তাদের ইউজারের তথ্য বাইরের কাউকে দিবে না )

সফটওয়্যার ফার্মটি দুইজন ডেভেলপার কে এই কাজের দায়িত্ব দিল । একজন ডেভেলপার ফেসবুকের ইউজারের তথ্য নিয়ে কাজ করবে অন্যজন টুইটারের ইউজারের তথ্য নিয়ে কাজ করবে ।

প্রথম ডেভেলপার ফেসবুকের ইউজারদের একটি List এ রেখে কাজটি করে ফেললো এইভাবেঃ

```java
public class Facebook
    {
        private List userList;
        public Facebook()
        {
            userList = new List();
            userList.Add("Logical Forhad");
            userList.Add("Rayhanur Rahman");
            userList.Add("Maruf Khan");
            userList.Add("Arif Raian");
            userList.Add("Mahedi Mahfuj");
            userList.Add("Atish Dipankar");
        }
          public List GetUserList()
         {
            return userList;
         }
    }
```

অন্যজন টুইটার ইউজার দের তথ্য গুলো একটি Array তে রেখে কাজটি করে ফেললো এইভাবেঃ

```java
public class Twitter
    {
        private String[] userList;
        private const int MAX_USER = 6;
        public Twitter()
        {
            userList = new string[MAX_USER];
            userList[0] = "_iLogical";
            userList[1] = "Amit Seal Ami";
            userList[2] = "Crackbrained Sakkhor";
            userList[3] = "Bazlur Rahman Rokon";
            userList[4] = "Sachine Tendulkar";
            userList[5] = "Shane Watson";
        }
 
        public String[] GetUserList()
        {
            return userList;
        }
    }
```

এরপর যখন তারা ফেসবুক এবং টুইটারের ইউজারদের তথ্য গুলো ক্লায়েন্ট কোডে দেখতে চাইলো তখন তারা নিচের কোডের মত করে দেখে নিলোঃ

```java
public class Program
    {
        static void Main(string[] args)
        {
            Facebook facebook = new Facebook();//concrete implementation
            List<string> fbuserList = facebook.GetUserList();
 
            Console.WriteLine("Facebook users........n");
 
            for (int i = 0; i < fbuserList.Count; i++)
            {
                Console.WriteLine(fbuserList[i]);
            }
            Console.WriteLine("n");
 
            Twitter twitter = new Twitter();//concrete implementation
            String[] twitterUserList = twitter.GetUserList();
            Console.WriteLine("Twitter users........n");
 
            for (int i = 0; i < twitterUserList.Length; i++)
            {
                Console.WriteLine(twitterUserList[i]);
            }
 
            Console.ReadKey();
        }
    }
```

এখন দুইজন ডেভেলপারই আলাদা আলাদা ডেটা স্ট্রাকচার ব্যবহার করেছে ফেসবুক এবং টুইটার এর ইউজারদের তথ্য রাখতে ।

অন্যদিকে  তারা তাদের কোড এর ডেটা স্ট্রাকচার চেঞ্জ করতে বিন্দু মাত্র আগ্রহী না। কিন্তু তারা যে ডিজাইন করেছে এই ডিজাইনে কিছু সমস্যা আছেঃ

আমরা টুইটার এবং ফেসবুকের কংক্রিট ইমপ্লিমেন্টেশন করছি, কিন্তু করার কথা ইন্টারফেস কোডিং , কেননা OOP বলে Code to interface not to implementation.
আমরা  ক্লায়েন্ট কোডে অনেকগুলো বয়লারপ্লেট কোড লিখে ফেলেছি । ফেসবুকের জন্য একবার এর ইউজারদের নাম লুপ চালিয়ে প্রিন্ট করেছি আর একবার টুইটারের জন্য ইউজারদের লিস্ট লুপ চালিয়ে প্রিন্ট করেছি ।মোদ্দা কথা একই কাজ দুই বার করেছি। গুগল প্লাসের ইউজারদের তথ্য প্রিন্ট করতে চাইলে আমাদের  আরেকটা লুপ যোগ করতে হবে ।
ক্লায়েন্ট কোডের জানতে হচ্ছে তাকে কত প্রকার ডেটাস্ট্রাকচারে লুপ ঘুরিয়ে ইউজারদের তথ্য প্রিন্ট করতে হবে। (যেমন গুগল প্লাস এর ইউজারদের তথ্য প্রিন্ট করতে চাইলে ক্লায়েন্ট কোড কে ফেসবুক+টুইটার+গুগল প্লাস = ৩ প্রকার ডেটাস্ট্রাকচার সম্পর্কে জানতে হবে ) যেটা OOP এর Encapsulation  রুল এর ভায়োলেশন ।
সমাধানঃ তাহলে আমরা এখন কি করতে পারি? চলুন একটু চিন্তা করি । আচ্ছা এরকম হলে কেমন হয় বলুন তো , একটি অবজেক্ট যদি যে কোনও প্রকার ডেটাস্ট্রাকচারের উপর কাজ করে সেই ডেটাস্ট্রাকচারের ডেটা গুলো প্রিন্ট করে দিতে পারে তাহলেই তো আমাদের কাজ হয়ে গেলো ।
তাহলে প্রথম ডেভেলপার এবং দ্বিতীয় ডেভেলপার অথবা অন্য যে কোনও ডেভেলপার যে ডেটাস্ট্রাকচার নিয়েই কাজ করুক না কেনো আমাদের ঐ একটি মাত্র অবজেক্ট সব ডেটাস্ট্রাকচারের উপর ছড়ি ঘুড়িয়ে আমাদের ডেটা গুলো প্রিন্ট করে দিতে পারে । হ্যা আমরা এখন এমনই একটি অবজেক্ট বানাবো ।

যেহেতু এটি সবার উপর কাজ করবে তাই এটিকে আমরা একটি ইন্টারফেস হিসেবেই রাখিঃ

```java
public interface ISocialNetworking
    {
        bool HasNext();
        Object Next();
    }
```

এখন আমরা দুটি class বানাবো যারা আমাদের সদ্য নতুন বানানো ইন্টারফেসকে ইমপ্লিমেন্ট করবে ।

ফেসবুকের জন্যঃ

```java
public class FacebookIterator : ISocialNetworking
    {
        private List<String> userList;
        private int _postion = 0;
        public FacebookIterator()
        {
            userList = new List<string>();
            userList.Add("Logical Forhad");
            userList.Add("Rayhanur Rahman");
            userList.Add("Maruf Khan");
            userList.Add("Arif Raian");
            userList.Add("Mahedi Mahfuj");
            userList.Add("Atish Dipankar");
        }
        public bool HasNext()
        {
            return userList.Count() > _postion;
        }
 
        public object Next()
        {
            String element = userList.ElementAt(_postion++);
 
            return element;
        }
    }
```

টুইটারের জন্যঃ

```java
public class TwitterIterator : ISocialNetworking
    {
        private String[] userList;
        private const int MAX_USER = 6;
        private int position = 0;
        public TwitterIterator()
        {
            userList = new string[MAX_USER];
            userList[0] = "_iLogical";
            userList[1] = "Amit Seal Ami";
            userList[2] = "Crackbrained Sakkhor";
            userList[3] = "Bazlur Rahman Rokon";
            userList[4] = "Sachine Tendulkar";
            userList[5] = "Shane Watson";
        }
 
        public bool HasNext()
        {
            return userList.Length > position;
        }
 
        public object Next()
        {
            String element = userList[position++];
            return element;
        }
    }
```

এখন আমাদের ক্লায়েন্ট কোডে আমরা ইন্টারফেসটিকে এভাবে ইউজ করতে পারিঃ

```java
public class Program
    {
        static void Main(string[] args)
        {
 
            Console.WriteLine("Facebook users........n");
            ISocialNetworking iterator = new FacebookIterator();
            while (iterator.HasNext())
            {
                Console.WriteLine(iterator.Next());
            }
            Console.WriteLine("n");
 
            iterator = new TwitterIterator();
            Console.WriteLine("Twitter users........n");
            while (iterator.HasNext())
            {
                Console.WriteLine(iterator.Next());
            }
            Console.ReadKey();
        }
    }
```

দাঁড়ান এখানেই শেষ না আমরা আমাদের ডিজাইনটিকে আরও ভালো করতে পারি । আমাদের Twitter class টীকে আমরা এভাবে মোডিফাই করতে পারি যেখানে আমরা একটি নতুন মেথড যোগ করেছি যার কাজ হচ্ছে আমাদের ইন্টারফেসটিকে(ISocialNetworking) রিটার্ন করা ।

সো মোডিফাইড Twitter class টি এরকমঃ

```java
public class Twitter
    {
        private String[] userList;
        private const int MAX_USER = 6;
        public Twitter()
        {
            userList = new string[MAX_USER];
            userList[0] = "_iLogical";
            userList[1] = "Amit Seal Ami";
            userList[2] = "Crackbrained Sakkhor";
            userList[3] = "Bazlur Rahman Rokon";
            userList[4] = "Sachine Tendulkar";
            userList[5] = "Shane Watson";
        }
 
        public ISocialNetworking GetIterator()
        {
            return new TwitterIterator();
        }
    }
```

এবং মোডিফাইড Facebook class টি হচ্ছে এরকমঃ

```java
public class Facebook
    {
        private List<String> userList;
        public Facebook()
        {
            userList = new List<string>();
            userList.Add("Logical Forhad");
            userList.Add("Rayhanur Rahman");
            userList.Add("Maruf Khan");
            userList.Add("Arif Raian");
            userList.Add("Mahedi Mahfuj");
            userList.Add("Atish Dipankar");
        }
 
        public ISocialNetworking GetIterator()
        {
            return new FacebookIterator();
        }
    }
```

এখন সবশেষে আমাদের ক্লায়েন্ট কোড চেঞ্জ হয়ে দাঁড়াবে এরকমঃ

```java
public class Program
    {
        static void Main(string[] args)
        {
            ISocialNetworking iterator;
 
            Facebook facebook = new Facebook();
            Twitter twitter = new Twitter();
 
            iterator = facebook.GetIterator();
 
            Console.WriteLine("Facebook users........n");
            while (iterator.HasNext())
            {
                Console.WriteLine(iterator.Next());
            }
            Console.WriteLine("n");
 
            iterator = twitter.GetIterator();
 
            Console.WriteLine("Twitter users........n");
            while (iterator.HasNext())
            {
                Console.WriteLine(iterator.Next());
            }
            Console.ReadKey();
        }
    }
```

ব্যস হয়ে গেলো আমাদের Iterator ডিজাইন প্যাটার্ন । কিন্তু আমরা এই ছোট্ট ডিজাইন প্যাটার্ন শিখতে গিয়ে নিজে নিজেই আমাদের Iterator বানিয়ে নিয়েছি যেটা প্রায় সব ল্যাঙ্গুয়েজেই আগে থেকেই বানিয়ে রাখা আছে।
.NET এ এই ইন্টারফেসটির নাম হচ্ছে IEnumerator। ডেটাস্ট্রাকচার যে রকমই হোক না কেনো ,এটা সব Collection এর উপর কাজ করতে পারে।

এটি ব্যবহার করলে আমাদের FacebookIterator ,TwitterIterator এবং ISocialNetworking এগুলো আর লাগবে না  কিন্তু Facebook এবং Twitter class এ কিছুটা চেঞ্জ করতে হবে ।

মোডিফাইড  ফেসবুকঃ

```java
public class Facebook
    {
        private List<String> userList;
        public Facebook()
        {
            userList = new List<string>();
            userList.Add("Logical Forhad");
            userList.Add("Rayhanur Rahman");
            userList.Add("Maruf Khan");
            userList.Add("Arif Raian");
            userList.Add("Mahedi Mahfuj");
            userList.Add("Atish Dipankar");
        }
 
        public IEnumerator GetIterator()//introducing IEnumurator
        {
            return userList.GetEnumerator();
        }
    }
```

মোডিফাইড টুইটারঃ

```java
public class Twitter
    {
        private String[] userList;
        private const int MAX_USER = 6;
        public Twitter()
        {
            userList = new string[MAX_USER];
            userList[0] = "_iLogical";
            userList[1] = "Amit Seal Ami";
            userList[2] = "Crackbrained Sakkhor";
            userList[3] = "Bazlur Rahman Rokon";
            userList[4] = "Sachine Tendulkar";
            userList[5] = "Shane Watson";
        }
 
        public IEnumerator GetIterator()//introducing IEnumurator
        {
            return userList.GetEnumerator();
        }
    }
```

এবং সবশেষে আমাদের ক্লায়েন্ট কোড সামান্য একটু মোডিফাইড হয়ে এরকম হবেঃ

```java
public class Program
    {
        static void Main(string[] args)
        {
            IEnumerator iterator;//changed 
 
            Facebook facebook = new Facebook();
            Twitter twitter = new Twitter();
 
            iterator = facebook.GetIterator();
 
            Console.WriteLine("Facebook users........n");
            while (iterator.MoveNext())
            {
                Console.WriteLine(iterator.Current);
            }
            Console.WriteLine("n");
 
            iterator = twitter.GetIterator();
 
            Console.WriteLine("Twitter users........n");
            while (iterator.MoveNext())
            {
                Console.WriteLine(iterator.Current);
            }
            Console.ReadKey();
        }
    }
```
    
তাহলে এই ছিলো আমাদের Iterator ডিজাইন প্যাটার্ন । খুবই সহজ এবং ছোট একটি ডিজাইন প্যাটার্ন যেটি যে কোনও Collection এর উপর হুকুমদারী করতে পারে :)

হ্যাপি কোডিং :)

