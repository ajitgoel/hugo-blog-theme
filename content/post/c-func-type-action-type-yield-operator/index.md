---
title: "C#: Func type, Action type, yield operator."
date: 2021-09-26T18:17:07.836Z
draft: false
featured: false
tags:
  - C#
categories:
  - C#
image:
  filename: ""
  focal_point: Smart
  preview_only: false
---
The **Func type** provides a way to store anonymous methods. For the Func type, the first type parameters are the arguments to the methods, and the final type parameter is the return value.

**Example:**

```
[TestClass]
public class UnitTest1
{
  [TestMethod]
  public void FuncTypeTest()
  {
    Func<string, bool> validator =word =>
    {
      var count = word.Length;
      return count > 4;
    };
    var isValid = validator("AjitGoel");
    Assert.IsTrue(isValid);
  }
}
```

**Action type** objects do not return a value. They are similar to a void method. A Func type on the other hand returns a result.

```
Action<int> action1 = (int counter) => Console.WriteLine("Write {0}", counter);
```

The **yield** keyword controls the iterator’s behavior. If the consumer requests an iterator to produce a value, it runs until a yield statement is encountered. At that point, the iterator is suspended until the consumer asks for the next value.

```
[TestClass]
public class FizzBuzzTest
{
  [TestMethod]
  public void Test1()
  {
    var iEnumerableString = new FizzBuzz().GenerateFizzBuzzUpto(100);
    var list = iEnumerableString.ToList();
    Assert.IsTrue(list[0] == "Fizz:3");
    Assert.IsTrue(list[1] == "Buzz:5");
    Assert.IsTrue(list[6] == "FizzBuzz:15");
  }
}
public class FizzBuzz
{
  public IEnumerable<string> GenerateFizzBuzzUpto(int numberUpto)
  {
    for (var counter = 1; counter < numberUpto; counter++)
    {
      if (counter%3 == 0 && counter%5 == 0)
      {
        yield return $"FizzBuzz:{counter}";
      }
      if (counter % 3 == 0)
      {
        yield return $"Fizz:{counter}";
      }
      if (counter % 5 == 0)
      {
        yield return $"Buzz:{counter}";
      }
    }
  }
}
```