# এডাপ্টার প্যাটার্ন  

এডাপ্টার ডিজাইন প্যাটার্ন খুবই সহজ একটি ডিজাইন প্যাটার্ন ।

বাস্তব জীবনেও আমরা বিভিন্ন ধরনের এডাপ্টার ব্যবহার করি । যেমন যে কোনও রকমের চার্জার , মোবাইল, ক্যামেরার মেমরী কার্ডের এডাপ্টর ইত্যাদি ।

তাহলে সফটওয়্যার ডিজাইনে এডাপ্টার প্যাটার্ন কি?

চলুন আমরা উদাহরণ এর মাধ্যমেই এর উত্তর খুজে নিই।

মনে করুন সেই প্রাগৈতিহাসিক যুগে যখন ডাইনোসররা ঘুরে বেড়াত তখন এক আদিমানব পেটের দায়ে একটি সফটওয়্যার ফার্মে কামলা খাটত । সেই আমলে তো আর এত এত লাইব্রেরী/ ফ্রেমওয়ার্ক ছিলো না । তো সেই আদি মানব সেই ফার্মে C# ল্যাঙ্গুয়েজে একটি class লিখেছিলো যেটা শুধুমাত্র একটি কাজ করতে পারত । সেটা হচ্ছে এই class এর একটা মেথড  দিয়ে সে একটা ইন্টিজার এর অ্যারের ইন্টিজার গুলোর যোগফল বের করে দিত।

Class টা হচ্ছে ঠিক এ রকমঃ

```java
public class Calculator
    {
        public int GetSum(int[] numbers)
        {
            int sum = numbers.Sum();
            return sum;
        }
    }
```

এরপর সেই আদি মানব তাহার ClientCode এ এই Calculator class টিকে আনন্দের সহিত ঠিক এভাবে ব্যবহার করতে লাগিলো

```java
public class Program
    {
        static void Main(string[] args)
        {
            var calculator = new Calculator();
            int[] numbers = { 1, 2, 3, 4, 5 };
            var sum = calculator.GetSum(numbers);
            Console.WriteLine(sum);
        }
    }
```

এরপর লক্ষ লক্ষ বছর পর প্রস্তর যুগের গাছের চামড়া পরা ইয়া লম্বা লম্বা দাঁড়ি ওয়ালা প্রোগ্রামার একটি গুহার ভেতরে সেই আদি মানবের ভাঙ্গাচুরা একটি পেন ড্রাইভ খুজে পেলো।

সে তার ল্যাপটপে পেনড্রাইভটি ঢুকিয়ে একটি DLL ফাইল খুজে পেলো। সে ঘেটেঘুটে দেখলো যে এই DLL এ সেই আদি মানবের বানানো class টী রয়েগেছে।

সে এই class কে তার একটি এপ্লিকেশনে ইউজ করার সিদ্ধান্ত নিলো।

ইউজ করতে গিয়ে দেখলো যে আদি মানবটি তার class এ Array ব্যবহার করেছে। কিন্তু তার এপ্লিকেশনে কোথাও Array এর ব্যবহার নেই। কারন এতদিনে এই প্রস্তর যুগে Collection Framework চলে এসেছে। List,Dictionary,HashTable এগুলোর ব্যবহার শুরু হয়েছে। এই প্রোগ্রামার সিদ্ধান্ত নিল যেভাবেই হোক আদি মানবের কোড তাকে তার এপ্লিকেশনে ব্যবহার করতে হবেই । যেভাবেই হোক।

ঐতিহ্য ধরে রাখার জন্য মরিয়া এই গাছের বাকল পড়া প্রোগ্রামার ।

এখন এই প্রোগ্রামার অনেক চিন্তা ভাবনা করে একটি বুদ্ধি বের করলো। সে একটা class লিখলো যেটা তার আদি মানবের class টাকে ব্যবহার করবে। ঠিক এভাবেঃ

```java
public class CalculatorAdapter
    {
        private readonly Calculator _calculator;
        public CalculatorAdapter()
        {
            _calculator =new Calculator();
        }
 
        public int GetTotalSum(List<int> numbers)
        {
            int sum = _calculator.GetSum(numbers.ToArray());
            return sum;
        }
    }
```

এখানে আমরা আসলে কি করেছি?? আমরা এখানে আদি মানবের বানানো Calculator class টাকে ইউজ করেছি। এর GetSum() মেথডটিকে GetTotalSum() মেথড এর মধ্য ব্যবহার করেছি। আর একটি কাজ এখানে করা হয়েছে সেটা হচ্ছে কনভার্সন । এখানে লক্ষ্যনীয় ব্যপার হচ্ছে GetSum() কিন্তু আর্গুমেন্ট হিসেবে একটি ইন্টিজারের Array নেয় আবার অন্যদিকে GetTotalSum() মেথড আর্গুমেন্ট হিসেবে একটি ইন্টিজারের লিস্ট নেয়।

যেহেতু আদি মানবের কোড ইন্টিজারের Array এর ইলিমেন্টের যোগফল বের করে সেহেতু এখানে ইন্টিজারের লিস্টকে Array তে কনভার্ট করা হয়েছে ।

তাহলে প্রস্তর যুগের প্রোগ্রামার এখন তার ClientCode এ নতুন কোড ইউজ করবে এইভাবেঃ  

```java
class Program
    {
        static void Main(string[] args)
        {
            CalculatorAdapter calculator = new CalculatorAdapter();
            List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };
            var sum = calculator.GetTotalSum(numbers);
            Console.WriteLine(sum);
        }
    }
```

প্রস্তর যুগের প্রোগ্রামার এতক্ষণ যা করলো সেটা হচ্ছে সে CalculatorAdapter নামে একটা Adapter লিখেছে Calculator class এর জন্য যাতে করে Calculator class এর কোনও কিছু চেঞ্জ না করে সে এটাকে ব্যবহার করতে পারে।

এখানে দেখা যাচ্ছে প্রোগ্রামারটি একটি লিস্ট পাঠাচ্ছে এর যোগফল বের করার জন্য কিন্তু ভেতরে সে আগের Array ব্যবহার করেই যোগফল বের করছে যেটা ClientCode থেকে বোঝা যাচ্ছে না।

এতক্ষণ যে আপনাদের বাস্তব এবং কল্পনীয় একটা গল্প শোনালাম এটাই হচ্ছে এডাপ্টর ডিজাইন প্যাটার্ন। গফ এর সংজ্ঞা দেখলেই ব্যপারটা ক্লিয়ার হয়ে যাবে আশাকরি ;)

**Convert the interface of a class into another interface clients expect. Adapter lets classes work together that couldn’t otherwise because of incompatible interfaces.**
