# চেইন অফ রেন্সপন্সিবিলিটি  

প্রত্যেকটা ডিজাইন প্যাটার্নেরই উদ্ভব হয়েছে মূলত এক একটি সমস্যার সমাধান সবচেয়ে ভালভাবে সমাধান করার  জন্য ।

সফটওয়্যার ইঞ্জিনিয়ারিং এ CodeReuse বলতে যেটা বোঝায় সেটা ডিজাইন প্যাটার্নই সবচেয়ে ভালভাবে সম্পন্ন করে । আমাদের দৈনন্দিন জীবনে নানা ভাবে ছড়িয়ে ছিটিয়ে আছে ডিজাইন প্যাটার্ন । একটু ভালভাবে খেয়াল করে চিন্তাকরলেই ধরাযায় প্যাটার্ন গুলো ।

এই যেমন কয়েক দিন আগেই আমার অফিসের সবাই ট্যুরে ভুটান ঘুরে এল । আমি যে প্রোজেক্টে ছিলাম সেটিও কমপ্লিট হয়ে গিয়েছিল । ভাবলাম এই ফাকে গ্রামে গিয়ে ঘুরে আসলে মন্দ হয় না । আমার প্রজেক্ট ম্যানেজারের কাছে ৫ দিনের ছুটির জন্য দরখাস্ত নিয়ে গেলাম । আমাদের প্রতি উইকে ওয়ার্কিং ডে হচ্ছে ৫ দিন । অফিসের নিয়ম হচ্ছে এক সপ্তাহ ছুটির জন্য ১ মাস আগে থেকে বলে রাখতে হয় । আমার প্রজেক্ট ম্যানেজার দরখাস্তে ৫ দিন দেখে বললেন ৩ দিন হলে আমি এখনই তোমাকে ছুটি দিয়ে দিতাম । যেহেতু তুমি ৫ দিন ছুটি চাচ্ছ সেহেতু তোমাকে রিসোর্স ম্যানেজারের কাছ থেকে ছুটি নিতে হবে । ৩ দিনের বেশি ছুটি দেয়া আমাদের এখতিয়ারে নেই।

আবার একজন বড় ভাই বললেন যে ১ দিনের ছুটি হলে তুমি তোমার টিম লিডারের কাছ থেকেই নিতে পারতে ।

এরপর আমি রিসোর্স ম্যানেজারের কাছ থেক দরখাস্ত নিয়ে গেলাম , উনি খুশি মনে আনন্দের সহিত ছুটি এপ্রুভ করলেন । আমিও খুশিতে ডগমগ হয়ে গ্রামে চলে গেলাম ।

তো ব্যাপারটা কি দাঁড়াল । আমার যদি ১ দিনের ছুটির দরকার হত আমি আমার টিম লিডারের কাছ থেকেই নিতে পারতাম । আবার ৩ দিনের ছুটির দরকার হলে প্রজেক্ট ম্যানেজারের কাছ থেকেই নিতে পারতাম । এর চেয়ে বেশি ছুটি হলে হয়ত এডমিন অথবা অন্য কোনও ঊর্ধ্বতন কর্মকর্তার কাছে থেকেই নিতে হত ।

চলুন আমরা কোডে জিনিসগুলো লিখে ফেলি । সবার প্রথমে আমাদের দরকার হবে একটি Response নামে enum এর । enum হচ্ছে জাস্ট একটা টাইপ । যে আমার রিকোয়েস্ট এপ্রুভড না ডিনায়েড ।

```java
public enum Response
{
 Approved,
 Denied
}
```

এখন আরেকটি ব্যাপার দেখা যাচ্ছে যে আমার টিম লিডার / প্রোজেক্ট ম্যানেজার অথবা রিসোর্স ম্যানেজার সবারই একটি ক্ষমতা হচ্ছে ছুটি Approve করা এবং রেজাল্ট দেয়া যে ছুটি মঞ্জুর না নামঞ্জুর । তাহলে যেহেতু সবারই একটা কমন বৈশিষ্ট হচ্ছে ছুটি Approve করা তাহলে আমরা একটি interface বানাই যার একটি মেথড থাকবে Approve নামে এবং এই মেথড একটি Response নামে enum রিটার্ন করবে এবং প্যারামিটার হিসেবে যাকে এপ্রুভ করবে তার একটা রেফারেন্স থাকবে  । এক্ষেত্রে Employee আমরা interface টির নাম দেই IApprover :

```java
public interface IApprover
    {
        Response Approve(Employee employee);
    }
```

আচ্ছা আমি যে দরখাস্তটি নিয়ে গিয়েছিলাম তার মধ্যে কি ছিলো ??ধরে নিলাম এর মধ্যে ছিলো আমার নাম আর কয়দিন ছুটি দরকার সেটা । আমি তো আর আমার নামে class লিখতে পারি না । এ জন্য class এর নাম দিলাম Employee .যার দুটি প্রপার্টি একটি হচ্ছে Name অন্যটি Days. Employee class টি ঠিক এরকমঃ

```java
public class Employee
    {
        public String Name { get; set; }
        public int Days { get; set; }
        public Employee(String name, int days)
        {
            Name = name;
            Days = days;
        }
    }
```

এখন আমরা এর আগে যে interface টা বানিয়ে ছিলাম সেই interface টা implement করবে আমার TeamLeader এবং ProjectManager এবং তাদের কাছে Employee এর একটা রেফারেন্স থাকবে কেননা আমি যে দরখাস্ত নিয়ে গিয়েছিলাম সেটি আসলে আমাকেই রিপ্রেজেন্ট করে ।

সো একদমে আমরা দেখে ফেলি  IApprover implement করার পর TeamLeader এবং ProjectManager class দুটো কেমন দেখায় । TeamLeader class টি এরকমঃ

```java
public class TeamLeader : IApprover
{
    public Response Approve(Employee employee)
    {
        if (employee.Days == 1)
            return Response.Approved;
        return Response.Denied;
    }
}
```

ProjectManager class টি এরকমঃ

```java
public class ProjectManager : IApprover
{
    public Response Approve(Employee employee)
    {
        if (employee.Days <= 3 && employee.Days >= 1)
            return Response.Approved;
        return Response.Denied;
    }
}
```

TeamLeader এবং ProjectManager উভয়ের Approve মেথডের মধ্য Employee এর কত দিন ছুটি দরকার তা চেক করেছি । যদি ছুটির দিন TeamLeader এর এখতিয়ার এর বাইরে হয় তাহলে তাহলে সে Denied রিটার্ন করবে আর এখতিয়ারে থাকলে Approved রিটার্ন করবে । এরকম ভাবে ProjectManager এর ক্ষেত্রেও প্রযোজ্য ।

এখন আমাদের ClientCode দেখা যাক ।

```java
public class Program
{
    public static void Main(string[] args)
    {
        Employee employee = new Employee("Forhad", 4);
 
        IApprover approver = new TeamLeader();
 
        Response responses = approver.Approve(employee);
 
        if (Response.Approved != responses)
        {
            approver = new ProjectManager();
            responses = approver.Approve(employee);
            if (Response.Approved == responses)
            {
                Console.WriteLine("Request Granted Have A nice journey Mr:{0}", employee.Name);
            }
            else
            {
                Console.WriteLine("Sorry Your Request was not granted  Mr:{0}", employee.Name);
            }
        }
        else
        {
            Console.WriteLine("Request Granted Have A nice journey Mr:{0}", employee.Name);
        }
 
    }
}
```

ClientCode এ আমরা আসলে কি করেছি??

আমরা Employee এর একটা অব্জেক্ট বানিয়েছি । OOP এর Inheritance ব্যবহার করে আমরা IApprover মধ্যে TeamLeader এর অবজেক্ট বানিয়ে রেখেছি । এর পর আমরা IApprover এর Approve() মেথড এর মধ্যে দিয়ে আগে Employee এর বানানো অবজেক্টটি রেফারেন্স হিসেবে Pass করে কল করেছি । আমরা জানি Approve মেথড Response নামক enum রিটার্ন করে । আমরা Response টা enum এ রেখেছি ।

এর পর চেক করেছি যদি Response টা Approved  হয় তাহলে আমরা ছুটি প্রাপ্ত Emplyee কে Greetings করেছি । আর Response টা Denied হলে আমরা ProjectManager এর কাছে গিয়েছি তার পর একই পদ্ধতিতে আমরা চেক করেছি তিনি ছুটি এপ্রুভ করেছেন না করেননি । এপ্রুভ করলে আবারও আমরা Employee কে Greetings জানিয়েছি অন্যদিকে Approve না হলে দুঃখ প্রকাশ করেছি ।

এখানে লক্ষণীয় বিষয় হচ্ছে ClientCode কে সব সময় জানতে হচ্ছে Employee কে প্রথমে কার কাছে ছুটির জন্য যেতে হবে , সে ছুটি দিতে না পারলে তারপর কার কাছে যেতে হবে সে না পারলে তারপর কার কাছে যেতে হবে ।

এই যে এতক্ষণ আমরা কোড করলাম এই ডিজাইন টা হচ্ছে বাজে একটা ডিজাইন । আমাদের নতুন  কারও কাছে যাওয়া লাগলে আমাদের ClientCode এ চেঞ্জ করতে হবে । আরও বেশ কয়েকটা মহা শত্রু কে আমদানি করতে হবে (মানে if else) J  । মনে করুন এমন একটা কোম্পানিতে ছুটি চাইতে গেলেন অথবা অন্য কোনও রিকোয়েস্ট এর জন্য গেলেন যেখানে এই রকম ১০০ জন ঊর্ধ্বতন কর্মকর্তার কাছে দৌড়াদৌড়ি করতে হতে পারে । ঐ কোম্পানির জন্য এই রকম একটা এপ্লিকেশন বানাতে গেলে আপনাকে ঐ ১০০ জন কর্মকর্তার অবজেক্ট বানাতে হবে প্রতিবারই যখনই আপনার দরকার হবে তাদের কাছ থেকে কোনও রেজাল্ট জানার জন্য হতে পারে সেটা ছুটি /লোন / বাজেট ইত্যাদি ইত্যাদি ।

এটা খুবই বাজে একটা ব্যাপার হবে ।

তাহলে আসুন আমরা আমাদের ডিজাইনটাকে একটু চেঞ্জ করি ।

আমরা IAppover ইন্টারফেসটাকে একটু চেঞ্জ করি । আমরা এখানে  ইন্টারফেসটার নিজেরই মত একটা  রেফারেন্স রেখে দিতে পারি যাতে করে যে এটাকে ইমপ্লিমেন্ট করবে সে যদি রিকোয়েস্ট গ্রান্ট করতে না পারে (এক্ষেত্রে ছুটি দেয়ার ব্যপারটা) সে অন্য একজনকে HandOver করে দিবে যে নে বাবা আমি পারলাম না , দেখ ছেলেটাকে তুই যদি ছুটি দিতে পারিস ।

এভাবে একজন না পারলে আরেকজন কে দিয়ে দিবে এভাবে চলতে থাকবে । মানে Responsibility এর Chaining হচ্ছে ।

তাহলে আমাদের IApprover ইন্টারফেসটা এরকমঃ

```java
public interface IApprover
{
    Response Approve(Employee employee);
    IApprover NextApprover { get; set; }
}
```

এখন তাহলে আমাদের

TeamLeader class এর ইমপ্লিমেনটেশন গিয়ে দাঁড়াবে এরকমঃ

```java
public class TeamLeader : IApprover
{
    public Response Approve(Employee employee)
    {
        if (employee.Days == 1)
            return Response.Approved;
        return NextApprover.Approve(employee);
    }
 
    public IApprover NextApprover { get; set; }
}
```

এখানে আমরা শুধু মাত্র TeamLeader যদি ছুটি এপ্রুভ করতে না পারে তাহলে সে NextApprover এর কাছে দায়িত্ব চাপিয়ে দিয়েছে NextApprover.Approve(employee);

এই লাইনটা দিয়ে ।

একই ভাবে ProjectManager এর ইমপ্লিমেন্টেশন গিয়ে দাঁড়াবে এরকমঃ

```java
public class ProjectManager : IApprover
{
    public Response Approve(Employee employee)
    {
        if (employee.Days <= 3 && employee.Days >= 1)
            return Response.Approved;
        return NextApprover.Approve(employee);
    }
    public IApprover NextApprover { get; set; }
}
```

একই ভাবে আমরা আরেকটি ResourceManager নামে class বানিয়ে ফেলি আমাদের উদাহরণ দেয়ার সুবিধার জন্য । এইভাবেঃ

```java
public class ResourceManager : IApprover
{
    public Response Approve(Employee employee)
    {
        if (employee.Days >= 7)
 
            return Response.Approved;
 
        return NextApprover.Approve(employee);
    }
    public IApprover NextApprover { get; set; }
}
```

এখন মনে করুন আমাদের অফিসে এমন একজন কে ঠিক করা হলো যে কিনা সবার রিকোয়েস্ট গ্রহন করবে / হ্যান্ডেল করবে । আমাদের আর TeamLeader,ProjectManager,ResourceManager এর কাছে যাওয়ার প্রয়োজন নেই । RequestHandler class এ আমাদের সব করে দিবে । আমরা শুধু তার কাছে গিয়ে আমদের প্রয়োজনের কথা বলব। সে কোথা থেকে / কার কাছ থেকে আমাদের রিকোয়েস্ট এপ্রুভ করায়ে আনবে তা আমাদের দেখার বিষয় না ।

আমাদের RequestHandler class টা এরকমঃ

```java
public class RequestHandler : IApprover
{
    public Response Approve(Employee employee)
    {
        return NextApprover.Approve(employee);
    }
    public IApprover NextApprover { get; set; }
}
```

এখানে লক্ষ্যনীয় ব্যপার হচ্ছে RequestHandler কিন্তু কোনও চেকিং না করেই নেক্সট এপ্রুভার এর কাছে এপ্রুভ করার জন্য রিকোয়েস্ট Redirect করছে ।

করছে এখন আমাদের চেইন ঠিক করে দেয়ার পালা । এখানে আমাদের যেটা করতে হবে সেটা হচ্ছে একজন রিকোয়েস্ট না এপ্রুভ করতে পারলে সে কার কাছে রিকোয়েস্ট টা পাঠাবে , আবার যার কাছে পাঠালো সে না পারলে কার কাছে পাঠাবে এভাবে চেইনিং চলতে থাকবে ।

আমরা একটা মেথড বানাই যে শুধুমাত্র চেইন তৈরি করে দিবেঃ

```java
private static IApprover CreateChain()
{
    IApprover handler = new RequestHandler();
    IApprover approver1 = new TeamLeader();
    IApprover approver2 = new ProjectManager();
    IApprover approver3 = new ResourceManager();
    handler.NextApprover = approver1;
    approver1.NextApprover = approver2;
    approver2.NextApprover = approver3;
    return handler;
}
```

এখানে আমরা প্রথমে RequestHandler এর অবজেক্ট বানিয়েছি যার কাছে আমরা রিকোয়েস্ট করব ছুটির জন্য । এর পর পরপর আমরা TeamLeader,ProjectManager,ResourceManager এর অবজেক্ট বানিয়েছি । এর পর চেইনিং এর পালা ।

Handler তার NextApprover সেট করবে (মানে রিকোয়েস্ট Redirect করবে )TeamLeader এর অবজেক্ট (approver1) এর কাছে , TeamLeader এপ্রুভ করতে না পারলে সে তার NextApprover সেট করবে (মানে রিকোয়েস্ট Redirect করবে ) ProjectManager এর কাছে , ঠিক একই ভাবে ProjectManager না পারলে করবে ResourceManager এর কাছে । এটাই হচ্ছে Chaining

এর পর একটা রেজাল্ট নিয়ে RequestHandler ব্যাক করবে যে তার কাছে কল করেছিলো তার কাছে ।

আমরা আমাদের পুরো ClientCode টা দেখে ফেলিঃ

```java
public class Program
{
    private static IApprover CreateChain()
    {
        IApprover handler = new RequestHandler();
        IApprover approver1 = new TeamLeader();
        IApprover approver2 = new ProjectManager();
        IApprover approver3 = new ResourceManager();
        handler.NextApprover = approver1;
        approver1.NextApprover = approver2;
        approver2.NextApprover = approver3;
        return handler;
    }
 
    public static void Main(string[] args)
    {
        var employee = new Employee("Forhad", 7);
 
        IApprover handler = CreateChain();
 
        Response response = handler.Approve(employee);
 
        if (response == Response.Approved)
        {
            Console.WriteLine("Have A nice Journey Mr:{0}", employee.Name);
        }
        else if (response == Response.Denied)
        {
            Console.WriteLine("Sorry Mr:{0} We are busy now", employee.Name);
        }
        Console.ReadLine();
    }
}
```

দেখে এখন প্রশ্ন হচ্ছে এই Chaining ভাঙবো কিভাবে ??

আমরা এই Chaining ভাঙবো একেবারে Chain এর সবার শেষে যে আছে সে যে

return NextApprover.Approve(employee); রিটার্ন  করেছিলো সেটা রিটার্ন না করে শুধুমাত্র Response.Denied যে রিটার্ন করলেই Chain ভেঙ্গে যাবে । আর চেইন ব্রেক না করলে সবার শেষের Chain এ যে থাকে সে তার Responsibility Chain এর পরের জনকে দিয়ে দিবে । তার পরে Chain এ তো কেঊ নেই । তাহলে আপনারা হেব্বি একটা Exception খাবেন (ট্রাই করেই দেখেন :) ) । একেবারে Null Exception :)

এই যে পুরো পোষ্ট জুড়ে আমরা যে Chain , Chain Responsibility , Responsibility করলাম এটাই হচ্ছে Chain Of Responsibility  ডিজাইন প্যাটার্ন

ভালো কথা ছুটি কিন্তু আমি পেয়েছিলাম ৫ দিন । 

ভুটানে যাওয়ার চেয়ে ৫ মাস পর আমার গ্রাম , আমার মায়ের মুখ দেখা আমার কাছে বেশি ভালোলাগার ছিলো :)

