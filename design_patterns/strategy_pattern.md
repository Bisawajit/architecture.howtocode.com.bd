# স্ট্র্যাটেজি প্যাটার্ন

স্ট্র্যাটেজি  প্যাটার্ন বেশ মজার এবং সহজ একটি ডিজাইন প্যাটার্ন । রানটাইমে কোনও এলগরিদম অথবা কোনও অবজেক্টের কমন বিহেভিওর চেঞ্জ করতে এই প্যাটার্নের জুড়ি নেই ।

মনে করুন একটি একশন গেমের কথা যেখানে একজন যোদ্ধাকে অনেক অনেক ভিলেনের সাথে যুদ্ধ করতে হয় । গেমের শুরুতে যোদ্ধার পাওয়ার / হেলথ থাকে ১০০% এবং শুরুতেই যোদ্ধা মার মার কাট কাট মুডে থাকে মানে এগ্রেসিভ মুডে থাকে । আস্তে আস্তে যোদ্ধার হেলথ কমতে থাকে এবং সে ডিফেন্সিভ মুডে চলে যায় । তখন সে খাবারের খোঁজ করতে থাকে, খাবার পেলে তার হেলথ আবার ১০০% হয়ে যায় এবং সে আবার এগ্রেসিভ মুডে চলে যায় ।

এ রকম গেমের জন্য যদি আমরা কোড লিখি তাহলে শুরুতেই একটা Fighter class বানাবো নিচের মত করেঃ

```
public class Fighter
    {
        public int Health { get; set; }
        public void ChangeMood(String mood)
        {
            switch (mood)
            {
                case "Angry":
                    SetAnggsiveBehavior();
                    break;
                case "Defensive":
                    SetDefensiveBehavior();
                    break;
            }
        }
        private void SetDefensiveBehavior()
        {
            Console.WriteLine("Defensive Mood");
        }
        private void SetAnggsiveBehavior()
        {
            Console.WriteLine("Angry Mood");
        }
    }
```

এখানে Health নামে একটি প্রপার্টি রাখা হয়েছে Fighter এর স্বাস্থ্যের জন্য । একটি ChangeMood() নামে মেথড রাখা হয়েছে এবং এর প্যারামিটার হিসেবে ফাইটারের মুড নামে একটি স্ট্রিং রাখা হয়েছে এবং একটি Switch ব্লক রাখা হয়েছে ।  Switch ব্লকের ভিতর চেক করা হয়েছে যদি ফাইটারের মুড Angry হয় তাহলে SetAnggsiveBehavior() কল করা হয়েছে । আবার যদি ফাইটারের মুড Defensive হয় তাহলে SetDefensiveBehavior() কল করা হয়েছে । দুইটা মেথডের মধ্যেই ফাইটারের বর্তমান মুডের জন্য কিছু একটা প্রিন্ট করে দেয়া হয়েছে কনসোলে । এরপর CllientCode আমাদের Fighter class এ ইউজ করবে এইভাবেঃ

```
class Program
    {
        static void Main(string[] args)
        {
            var fighter = new Fighter();
            var random = new Random();

            fighter.Health = random.Next(1, 100);

            if (fighter.Health <= 50)
            {
                fighter.ChangeMood("Defensive");
            }
            else if (fighter.Health > 50)
            {
                fighter.ChangeMood("Angry");
            }

            Console.ReadKey();
        }
    }
```

এখানে ফাইটারের স্বাস্থ্য ৫০ এর উপরে হলে আমরা ফাইটারকে এগ্রেসিভ মুডে নিয়ে গিয়েছি এবং ৫০ এর নিচে হলেই আমরা তাকে ডিফেন্সিভ মুডে নিয়ে গিয়েছি ।

ব্যস আমাদের গেমের জন্য কোড লিখা শেষ । কিন্তু এই কোডের class library যদি আমি অন্য কাউকে দেই এবং সে ফাইটারের জন্য নতুন কোনও Mood বানাতে চায় তাহলে সে পারবেনা । তাকে আমাদের ফাইটার ক্লাসের পুরো সোর্স কোড দিয়ে দিতে হবে । তাহলে আমাদের অবজেক্ট ওরিয়েন্টেড প্রোগ্রামিং এর OCP (Open Close Principle ) এর ভায়োলেশন হবে । OCP মানে হচ্ছে একটি class/entity শুধুমাত্র এক্সটেন্ড করার জন্য ওপেন থাকবে কিন্তু মোডিফিকেশন করার জন্য ক্লোজড থাকবে ।

কিন্তু বর্তমান Fighter class এ নতুন মুড ঢোকাতে চাইলে আমাদের switch ব্লকে নতুন case যোগ করতে হবে এবং নতুন একটি মেথড কল করতে হবে । তাই এটি OCP এর ভায়োলেশন ।

আবার এই ডিজাইনের আরোও সমস্যা হচ্ছে এটি Principle of Least Knowledge রুলেরও ভায়োলেশন । কেননা Fighter class কে সব সময় জানতে হচ্ছে তার কোন কোন মুড থাকতে পারে অর্থাৎ এগ্রেসিভ, ডিফেন্সিভ ব্লা ব্লা ব্লা এসব মুড সম্পর্কে তাকে জানতে হচ্ছে । কিন্তু আমাদের ফাইটার এর এসব সম্পর্কে নলেজ না থাকলেও চলে । আমরা তাকে রানটাইমে বলে দিতে পারি তুমি এখন এই মুডে ফাইট কর ।

সবশেষে এই ডিজাইন এর আরেকটি সমস্যা হচ্ছে SRP (Single Responsibility Principle) এর ভায়োলেশন । কেননা একটা ফাইটারের বিভিন্ন মুডের জন্য আমরা বিভিন্ন মেথড কল করেছি । কিন্তু বাস্তবে এই মুডগুলোর কোড অনেক বড় হয়ে যেতে পারে । আমরা এখানে শুধু মাত্র কনসোলে কিছু মেসেজ প্রিন্ট করেছি ।

তাই ডিফেন্সিভ মুড অথবা এগ্রেসিভ মুড মেথডে না নিয়ে আমরা এগুলো আলাদা class এ নিয়ে যেতে পারি ।

তাহলে আসুন আমরা কোডের ডিজাইন চেঞ্জ করে ফেলি।

যেহেতু সব ফাইটারের কমন একটি বৈশিষ্ট হচ্ছে ফাইট করা সেহেতু আমরা একটি কমন ইন্টারফেস IFighter বানাতে পারি যার মধ্যে একটি মেথড থাকবে Fight() নামেঃ

```
public interface IFighter
 {
     void Fight();
 }
```

এখন আমরা  আমাদের ফাইটারের এগ্রেসিভ এবং ডিফেন্সিভ মুডের জন্য দুইটা আলাদা আলদা class বানাতে পারি যারা উভয়েই IFighter এ ইমপ্লিমেন্ট করবে ।

এগ্রেসিভ এর জন্যঃ

```
public class Aggresive : IFighter
{
    public void Fight()
    {
        Console.WriteLine("Fighter is now in aggresive mood");
    }
}
```

ডিফেন্সিভ এর জন্যঃ

```
public class Defensive : IFighter
    {
        public void Fight()
        {
            Console.WriteLine("Fighter is now in defensie mood");
        }
    }
```

এভাবে আমাদের ডিজাইনের SRP এর প্রব্লেম সল্ভ হয়ে গেলো।

এখন  Principle of Least Knowledge এবং OCP এর প্রব্লেম সল্ভ করার জন্য আমাদের Fighter class টিকে মোডিফিকেশন করতে হবে এভাবেঃ

```
public class Fighter
    {
        public int Health { get; set; }
        private IFighter _fighter;
        public void ChangeMood(IFighter fighter)
        {
            _fighter = fighter;
            _fighter.Fight();
        }

    }
```

এখন আমাদের ClientCode দেখতে হবে এরকমঃ

```
class Program
    {
        static void Main(string[] args)
        {
            var fighter = new Fighter();
            var random = new Random();

            fighter.Health = random.Next(1, 100);

            if (fighter.Health <= 50)
            {
                fighter.ChangeMood(new Defensive());
            }
            else if (fighter.Health > 50)
            {
                fighter.ChangeMood(new Aggresive());
            }

            Console.ReadKey();
        }
    }
```

এই ডিজাইনে আমাদের সুবিধা কি তাহলে ।

আমরা যদি এই ডিজাইনের class library অন্য কাউকে দিয়ে দেই এবং সে যদি ফাইটারের জন্য নতুন কোনও মুড বানাতে চায়  তাহলে তাকে কখনই Fighter class এ হাত দিতে হবে না ।

তাকে নতুন মুডের জন্য class বানাতে হবে যেটি IFighter কে ইমপ্লিমেন্ট করবে , এবং Fight() মেথডের মধ্যে নতুন মুডের জন্য কোড লিখবে সবশেষে ক্লায়েন্ট কোডে এর Fighter এর ChangeMood() এর ভেতর দিয়ে  নতুন অবজেক্ট দিয়ে দিলেই আমাদের নতুন মুড এ ফাইট করা শুরু করবে ফাইটার ।

স্ট্র্যাটেজি প্যাটার্নের সবচেয়ে বড় সুবিধা হচ্ছে রানটাইমে কোনও কমন অবজেক্টের কমন বৈশিষ্ট / এলগরিদম(যেমন Sorting) চেঞ্জ করা এবং switch ব্লক অথবা if else এর শেকল থেকে কোডকে মুক্তি দেয়া ।

গফ এর ভাষ্য অনুযায়ী স্ট্র্যাটেজি প্যাটার্নের সংজ্ঞা এরকমঃ

**Define a family of algorithms, encapsulate each one, and make them interchangeable. Strategy lets the algorithm vary independently from clients that use it.**
