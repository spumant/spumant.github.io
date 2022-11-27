集合（Collection）类是专门用于数据存储和检索的类。这些类提供了对栈（stack）、队列（queue）、列表（list）和哈希表（hash table）的支持。大多数集合类实现了相同的接口。

集合（Collection）类服务于不同的目的，如为元素动态分配内存，基于索引访问列表项等等。这些类创建 Object 类的对象的集合。在 C# 中，Object 类是所有数据类型的基类。
## C# 动态数组（ArrayList）
动态数组（ArrayList）代表了可被单独索引的对象的有序集合。它基本上可以替代一个数组。但是，与数组不同的是，您可以使用索引在指定的位置添加和移除项目，动态数组会自动重新调整它的大小。它也允许在列表中进行动态内存分配、增加、搜索、排序各项。
![cs25.png](https://cdn.acwing.com/media/article/image/2022/11/12/192601_9c59425462-cs25.png) 
下面的实例演示了动态数组（ArrayList）的概念：
```
using System;
using System.Collections;

namespace CollectionApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            ArrayList al = new ArrayList();

            Console.WriteLine("Adding some numbers:");
            al.Add(45);
            al.Add(78);
            al.Add(33);
            al.Add(56);
            al.Add(12);
            al.Add(23);
            al.Add(9);
           
            Console.WriteLine("Capacity: {0} ", al.Capacity);
            Console.WriteLine("Count: {0}", al.Count);
                     
            Console.Write("Content: ");
            foreach (int i in al)
            {
                Console.Write(i + " ");
            }
            Console.WriteLine();
            Console.Write("Sorted Content: ");
            al.Sort();
            foreach (int i in al)
            {
                Console.Write(i + " ");
            }
            Console.WriteLine();
            Console.ReadKey();
        }
    }
}


```
当上面的代码被编译和执行时，它会产生下列结果：
```
Adding some numbers:
Capacity: 8 
Count: 7
Content: 45 78 33 56 12 23 9 
Sorted Content: 9 12 23 33 45 56 78 
```
## C# 哈希表（Hashtable）
Hashtable 类代表了一系列基于键的哈希代码组织起来的键/值对。它使用键来访问集合中的元素。

当您使用键访问元素时，则使用哈希表，而且您可以识别一个有用的键值。哈希表中的每一项都有一个键/值对。键用于访问集合中的项目。
![cs26.png](https://cdn.acwing.com/media/article/image/2022/11/12/192601_efc1c8b662-cs26.png) 
下面的实例演示了哈希表（Hashtable）的概念：
```
using System;
using System.Collections;

namespace CollectionsApplication
{
   class Program
   {
      static void Main(string[] args)
      {
         Hashtable ht = new Hashtable();


         ht.Add("001", "Zara Ali");
         ht.Add("002", "Abida Rehman");
         ht.Add("003", "Joe Holzner");
         ht.Add("004", "Mausam Benazir Nur");
         ht.Add("005", "M. Amlan");
         ht.Add("006", "M. Arif");
         ht.Add("007", "Ritesh Saikia");

         if (ht.ContainsValue("Nuha Ali"))
         {
            Console.WriteLine("This student name is already in the list");
         }
         else
         {
            ht.Add("008", "Nuha Ali");
         }
         // 获取键的集合
         ICollection key = ht.Keys;

         foreach (string k in key)
         {
            Console.WriteLine(k + ": " + ht[k]);
         }
         Console.ReadKey();
      }
   }
}

```
当上面的代码被编译和执行时，它会产生下列结果：
```
007: Ritesh Saikia
004: Mausam Benazir Nur
005: M. Amlan
008: Nuha Ali
002: Abida Rehman
003: Joe Holzner
001: Zara Ali
006: M. Arif

```
## C# 排序列表（SortedList）
SortedList 类代表了一系列按照键来排序的键/值对，这些键值对可以通过键和索引来访问。

排序列表是数组和哈希表的组合。它包含一个可使用键或索引访问各项的列表。如果您使用索引访问各项，则它是一个动态数组（ArrayList），如果您使用键访问各项，则它是一个哈希表（Hashtable）。集合中的各项总是按键值排序。
![cs27.png](https://cdn.acwing.com/media/article/image/2022/11/12/192601_5d05353b62-cs27.png) 
下面的实例演示了排序列表（SortedList）的概念：
```
using System;
using System.Collections;

namespace CollectionsApplication
{
   class Program
   {
      static void Main(string[] args)
      {
         SortedList sl = new SortedList();

         sl.Add("001", "Zara Ali");
         sl.Add("002", "Abida Rehman");
         sl.Add("003", "Joe Holzner");
         sl.Add("004", "Mausam Benazir Nur");
         sl.Add("005", "M. Amlan");
         sl.Add("006", "M. Arif");
         sl.Add("007", "Ritesh Saikia");

         if (sl.ContainsValue("Nuha Ali"))
         {
            Console.WriteLine("This student name is already in the list");
         }
         else
         {
            sl.Add("008", "Nuha Ali");
         }

         // 获取键的集合
         ICollection key = sl.Keys;

         foreach (string k in key)
         {
            Console.WriteLine(k + ": " + sl[k]);
         }
      }
   }
}

```
当上面的代码被编译和执行时，它会产生下列结果：
```
001: Zara Ali
002: Abida Rehman
003: Joe Holzner
004: Mausam Banazir Nur
005: M. Amlan 
006: M. Arif
007: Ritesh Saikia
008: Nuha Ali
```
## C# 堆栈（Stack）
堆栈（Stack）代表了一个后进先出的对象集合。当您需要对各项进行后进先出的访问时，则使用堆栈。当您在列表中添加一项，称为推入元素，当您从列表中移除一项时，称为弹出元素。
![cs28.png](https://cdn.acwing.com/media/article/image/2022/11/12/192601_aacc5c7562-cs28.png) 
下面的实例演示了堆栈（Stack）的使用：
```
using System;
using System.Collections;

namespace CollectionsApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            Stack st = new Stack();

            st.Push('A');
            st.Push('M');
            st.Push('G');
            st.Push('W');
           
            Console.WriteLine("Current stack: ");
            foreach (char c in st)
            {
                Console.Write(c + " ");
            }
            Console.WriteLine();
           
            st.Push('V');
            st.Push('H');
            Console.WriteLine("The next poppable value in stack: {0}",
            st.Peek());
            Console.WriteLine("Current stack: ");          
            foreach (char c in st)
            {
               Console.Write(c + " ");
            }
            Console.WriteLine();

            Console.WriteLine("Removing values ");
            st.Pop();
            st.Pop();
            st.Pop();
           
            Console.WriteLine("Current stack: ");
            foreach (char c in st)
            {
               Console.Write(c + " ");
            }
        }
    }
}

```
当上面的代码被编译和执行时，它会产生下列结果：
```
Current stack: 
W G M A
The next poppable value in stack: H
Current stack: 
H V W G M A
Removing values
Current stack: 
G M A
```
## C# 队列（Queue）
队列（Queue）代表了一个先进先出的对象集合。当您需要对各项进行先进先出的访问时，则使用队列。当您在列表中添加一项，称为入队，当您从列表中移除一项时，称为出队。
![cs29.png](https://cdn.acwing.com/media/article/image/2022/11/12/192601_ec0965e862-cs29.png) 
下面的实例演示了队列（Queue）的使用：
```
using System;
using System.Collections;

namespace CollectionsApplication
{
   class Program
   {
      static void Main(string[] args)
      {
         Queue q = new Queue();

         q.Enqueue('A');
         q.Enqueue('M');
         q.Enqueue('G');
         q.Enqueue('W');
         
         Console.WriteLine("Current queue: ");
         foreach (char c in q)
            Console.Write(c + " ");
         Console.WriteLine();
         q.Enqueue('V');
         q.Enqueue('H');
         Console.WriteLine("Current queue: ");        
         foreach (char c in q)
            Console.Write(c + " ");
         Console.WriteLine();
         Console.WriteLine("Removing some values ");
         char ch = (char)q.Dequeue();
         Console.WriteLine("The removed value: {0}", ch);
         ch = (char)q.Dequeue();
         Console.WriteLine("The removed value: {0}", ch);
         Console.ReadKey();
      }
   }
}

```
当上面的代码被编译和执行时，它会产生下列结果：
```
Current queue: 
A M G W 
Current queue: 
A M G W V H 
Removing values
The removed value: A
The removed value: M
```