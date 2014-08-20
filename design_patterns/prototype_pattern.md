# প্রোটোটাইপ প্যাটার্ন  

একটা ব্যাপার আমরা সবাই জানি যে, আমরা যখন new ইউজ করে অবজেক্ট বানাই তখন সাথে সাথে অবজেক্টটি মেমরীতে জায়গা দখল করে বসে ।

সো হেভী ওয়েট এপ্লিকেশনে অনেক অনেক অবজেক্টের এর দরকার হয় আমাদের । যত বেশী অবজেক্ট তত বেশী মেমরী খরচ । তবে বর্তমান সময়ের পিসি গুলার পারফরমেন্স এত ভালো যে অনেকেই এই জিনিসগুলো এড়িয়ে যায় অথবা অনেকের এপ্লিকেশনের মেমরী কমজিউম করার ব্যপারগুলো মুখ্য বিষয় হয়ে দাঁড়ায় না ।

তবে চিন্তা করুন মোবাইল গেম অথবা যে কোনও গেমের কথা । মেমরী কম কিন্তু এই মেমরী ইউজ করেই গেম বানাতে হবে । তখন এই প্রোটোটাইপ ডিজাইন প্যাটার্ন আমাদের অনেক সাহায্য করে ।

মোটিভেশনাল উদাহরনঃ

মনে করুন আমাদের একটি Person class আছে এরকমঃ

```java
public class Person
    {
        public String Name { get; set; }
        public String Email { get; set; }
    }
```

এখন আমাদের ClientCode এ আমরা এর অবজেক্ট বানাই এভাবেঃ

```java
public static void Main(string[] args)
  {
      var person = new Person();
      person.Name = "Person1";
      person.Email = "xyz@mail.com";
      Console.WriteLine("Name:{0} and Email:{1}", person.Name, person.Email);
 
  }
```

এখন এই অবজেক্ট যদি আমাদের এপ্লিকেশনের ১০০০ জায়গায় আবার প্রয়োজন হয় তবে আবার একই ভাবে আমাদের অবজেক্ট বানাতে হবে ১০০০ বার । অর্থাৎ ১০০০ বার আমাদের new করে অবজেক্ট বানাতে হবে । ১০০০ টি অবজেক্ট আমাদের মেমরীতে অনেক অনেক জায়গা নিয়ে চুপ করে বসে থাকবে । যেটি খুব ভালো কথা না ।

সমাধানঃ এই বার বার অবেজক্ট বানানোর হাত থেকে রেহাই পেতে আমরা যা করতে পারি তা হচ্ছে একটি অবজেক্টের ক্লোন বানিয়ে রেখে দিতে পারি । যখনই ঐ অবজেক্টের দরকার হবে ঠিক তখনই আমরা এর ক্লোন ইউজ করতে পারবো । আমাদের নতুন করে আর অবজেক্ট বানানোর দরকার হবে না ।

তাহলে চলুন আমরা Person class এর চেহারার মানচিত্র একটু চেঞ্জ করে ফেলি :P । তার আগে আমাদের একটি ইন্টারফেস এর দরকার হবে । আমাদের ইন্টারফেসটা সিম্পল এবং এরকমঃ

```java
public interface IClone
    {
        Object Clone();
    }

```

ক্লোনিং এর উপায় নম্বর ১:

এর আগে আমরা একটা class বানিয়েছিলাম যা ছিল খুবই সিম্পল । এর মাত্র দুটো মেম্বার ভেরিয়েবল ছিলো ।

এখন আমাদের যা করতে হবে তা হচ্ছে Person class টি IClone ইন্টারফেস কে ইমপ্লিমেন্ট করবে এবং ইন্টারফেস এর Clone মেথডের মধ্যে Person এর নতুন অবজেক্ট বানিয়ে এই অবজেক্টের একটা একটা করে প্রোপার্টি ধরে ধরে আমাদের Person class এর প্রোপার্টি গুলো এসাইন করতে হবে । নিচের উদাহরণ দেখলেই ব্যপারটা ক্লিয়ার হয়ে যাবেঃ

```java
public class Person : IClone
    {
        public String Name { get; set; }
        public String Email { get; set; }
        public Object Clone()
        {
            var person = new Person
            {
                Name = Name,
                Email = Email
            };
 
            return person;
        }
    }
```

এখন আমাদের ক্লায়েন্ট কোড দেখে নিইঃ

```java
public class Program
    {
        public static void Main(string[] args)
        {
            Person person = new Person();
            person.Name = "Person";
            person.Email = "xyz@mail.com";
 
            Console.WriteLine("Before Cloning.......");
            Console.WriteLine("Name:{0} and Email:{1}", person.Name, person.Email);
 
            Person person1 = person.Clone() as Person;
            Console.WriteLine("After Cloning..............");
            Console.WriteLine("Name:{0} and Email:{1}", person1.Name, person1.Email);
        }
    }
```

প্রোগ্রামটি রান করলে  আমরা দেখতে পাব যে শুধুমাত্র একবার অবজেক্ট বানিয়ে এর ক্লোন করে আমরা হুবুহু আরেকটি অবজেক্ট পেয়ে গেছি ।

ক্লোনিং এর উপায় নম্বর ২:

আমাদের Person class এ মাত্র দুটি প্রোপার্টি ছিলো । এবং Clone মেথডে আমরা এর অবজেক্ট বানিয়ে এই দুটি প্রোপার্টি এসাইন করেছিলাম । এখন যদি এমন হয় যে এই class এ অনেকগুলো প্রোপার্টি থাকবে । তাহলে  আমাদের ধরে ধরে এই অসংখ্য প্রোপার্টি গুলো এসাইন করতে হবে যা অনেকটা কষ্ট সাধ্য কাজ।

.NET আমাদের একটা সুবিধা দিয়েছে MemberwiseClone()ক্লোন করার সুবিধা । এই মেথড হচ্ছে অবজেক্টের আদিপিতা Object class এর একটি Protected মেথড । এটি যা করে তা হচ্ছে এটি অবজেক্টের সবগুলো মেম্বার এর ক্লোন করে রেখে দেয় ।

এটি ব্যবহার করতে হয় এভাবেঃ

```java
public class Person : IClone
    {
        public String Name { get; set; }
        public String Email { get; set; }
        public Object Clone()
        {
            return MemberwiseClone();
        }
    }
```

এবং আমাদের ক্লায়েন্ট কোড আগের মতই থাকবেঃ

```java
public class Program
    {
        public static void Main(string[] args)
        {
            Person person = new Person();
            person.Name = "Person";
            person.Email = "xyz@mail.com";
 
            Console.WriteLine("Before Cloning.......");
            Console.WriteLine("Name:{0} and Email:{1}", person.Name, person.Email);
 
            Person person1 = person.Clone() as Person;
            Console.WriteLine("After Cloning..............");
            Console.WriteLine("Name:{0} and Email:{1}", person1.Name, person1.Email);
        }
    }
```

এরপর আমাদের দুটো জিনিস নিয়ে একটু আলোচনা করা দরকার । একটি হচ্ছে ShallowCopy আর অন্যটি হচ্ছে DeepCopy.

তো ShallowCopy টা কি ?? আসুন জেনে নিইঃ-

ShallowCopy আর কিছুই না, একটা অবজেক্ট এর প্রত্যকেটা প্রোপার্টি কে অন্য অবজেক্ট এর প্রোপার্টিতে এসাইন করে দেয়াই হচ্ছে ShallowCopy । কিন্তু যখন আমরা একটা অবজেক্ট এর মধ্যে অন্য একটি অবজেক্ট এর রেফারেন্স রাখব তখন এই ShallowCopy অন্য রকম বিহেভ করে । চলুন উদাহরন দেখেই ব্যপার টা ক্লিয়ার করে নিই।

আমরা নতুন একটি class বানাই Address নামে যার একটি মাত্র প্রোপার্টি থাকবে Street নামেঃ

```java
public class Address
    {
        public String Street { get; set; }
    }
```

এবং আমাদের আগের Person class টি চেঞ্জ করে নিচের মত করিঃ

```java
public class Person : IClone
    {
        public String Name { get; set; }
        public String Email { get; set; }
        public Address Address { get; set; }
        public Person()
        {
            Address = new Address();
        }
        public Object Clone()
        {
            return MemberwiseClone();
        }
    }
```

তাহলে আমাদের Person class এর মধ্যে Address class এর একটি রেফারেন্স থাকলো ।

এখন আমরা আমাদের ক্লায়েন্ট কোডে অবজেক্ট ক্লোনিং এর আগে Person এর Address এসাইন করি এবং ক্লোনিং এর পর আবার আমরা ক্লোনড অবজেক্ট এর Address এসাইন করি ।

সবশেষে, ক্লোনিং এর আগের অবজেক্ট এর Address কনসোলে প্রিন্ট করলে দেখতে পাব যে আমরা পরে যে ক্লোনড অবজেক্ট এর Address এর ভ্যালু এসাইন করেছিলাম সেটা দেখাচ্ছে ।

```java
public class Program
    {
        public static void Main(string[] args)
        {
            Person person = new Person();
 
            person.Name = "Person";
            person.Email = "xyz@mail.com";
            person.Address.Street = "221B";
 
            Person person1 = person.Clone() as Person;
            person1.Address.Street = "221B Baker Street";
 
            Console.WriteLine("After Cloning..............");
            /*showing person1's Address in person object*/
            Console.WriteLine("Street:{0}",person.Address.Street);
        }
    }
```

তারমানে আমাদের ক্লোনড অবজেক্ট এর রেফারেন্স অবজেক্ট Address এবং একচুয়াল অবজেক্ট এর রেফারেন্স অবজেক্ট Address একই অবজেক্ট কে মানে Address কে পয়েন্ট করছে । যেটি একটি সমস্যা ।

আরেকটি বিষয় লক্ষ্যনীয় এখানে বাকি ভ্যারিয়েবলগুলো কিন্তু ঠিকই ক্লোন হচ্ছে কিন্তু রেফারেন্স ক্লোন হচ্ছে না । এর সমাধান পেতে পারি আমরা DeepCloning করে।

DeepCloning:

DeepCloning হচ্ছে  MemberwiseClone() এর সাথে বাড়তি কিছু যোগ করা । সেটা হচ্ছে অবজেক্ট এর রেফারেন্সটাও কপি করে অবজেক্ট রিটার্ন করা

ঠিক এভাবেঃ

```java
public class Person : IClone
    {
        public String Name { get; set; }
        public String Email { get; set; }
        public Address Address { get; set; }
        public Person()
        {
            Address = new Address();
        }
        public Object Clone()
        {
            //cloning member variable
            Person person = this.MemberwiseClone() as Person;
 
            //cloning reference object
            person.Address = new Address(); 
            person.Address.Street = this.Address.Street;
            return person;
        }
    }
```

এখন আমাদের ক্লায়েন্ট কোড দেখে আসিঃ

```java
public class Program
    {
        public static void Main(string[] args)
        {
            Person person = new Person();
 
            person.Name = "Person";
            person.Email = "xyz@mail.com";
            person.Address.Street = "221B";
 
            Person person1 = person.Clone() as Person;
            person1.Name = "Person";
            person1.Email = "xyz@mail.com";
            person1.Address.Street = "221B Baker Street";
 
            Console.WriteLine("Before Cloning.......");
            Console.WriteLine("Name:{0} and Email:{1}, Street:{2}", person.Name, person.Email, person.Address.Street);
 
            Console.WriteLine("After Cloning.......");
            Console.WriteLine("Name:{0} and Email:{1}, Street:{2}", person1.Name, person1.Email, person1.Address.Street);
        }
    }
```

এটা রান করলে ShallowCopy এর ঝামেলা আর থাকবে না অর্থাৎ ক্লোন করলে একই অবজেক্ট এর আরেকটি আইডেন্টিকাল অবজেক্ট পাব (দুটি ভিন্ন ভিন্ন অবজেক্ট পাব)।

আমরা আরেক উপায়ে DeepCopy করতে পারি । তার আগে আমাদের সব গুলো class এর উপর [Serializable] এট্রিবিউট লিখে রেখে আসতে হবে এবং Person class এর clone() মেথডে একটু অন্যভাবে কোড লিখতে হবে।

চলুন দেখে আসি । আমাদের [Serializable] এট্রিবিউট যোগ করার পর Address class টা এরকম দেখায়ঃ

```
[Serializable]
public class Address
{
    public String Street { get; set; }
}
```

এরপর মোডিফিকেশন এবং  [Serializable] এট্রিবিউট যোগ করার পর Person class টা এরকম দেখায়ঃ

```java
[Serializable]
    public class Person : IClone
    {
        public String Name { get; set; }
        public String Email { get; set; }
        public Address Address { get; set; }
        public Person()
        {
            Address = new Address();
        }
        public Object Clone()
        {
            using (Stream stream = new MemoryStream())
            {
                var binaryFormatter = new BinaryFormatter();
                binaryFormatter.Serialize(stream, new Person());
                stream.Seek(0, SeekOrigin.Begin);
                return (Person)binaryFormatter.Deserialize(stream);
            }
        }
    }
```

তাহলে আমাদের ক্লায়েন্ট কোড আগের মতই থাকবে, রেজাল্টও আগের মত দেখাবে । আমরা শুধুমাত্র DeepCopy করার কৌশলটা চেঞ্জ করেছি মাত্র ।

ক্লায়েন্ট কোডঃ

```java
public class Program
    {
        public static void Main(string[] args)
        {
            Person person = new Person();
 
            person.Name = "Person";
            person.Email = "xyz@mail.com";
            person.Address.Street = "221B";
 
            Person person1 = person.Clone() as Person;
            person1.Name = "Person";
            person1.Email = "xyz@mail.com";
            person1.Address.Street = "221B Baker Street";
 
            Console.WriteLine("Before Cloning.......");
            Console.WriteLine("Name:{0} and Email:{1}, Street:{2}", person.Name, person.Email, person.Address.Street);
 
            Console.WriteLine("After Cloning.......");
            Console.WriteLine("Name:{0} and Email:{1}, Street:{2}", person1.Name, person1.Email, person1.Address.Street);
        }
    }
```

এই ছিলো আমাদের Prototype Design Pattern ।

সমাপ্ত :)    

