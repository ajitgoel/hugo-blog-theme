---
title: ".Net Core: async-await tips"
date: 2021-09-26T17:36:58.907Z
draft: false
featured: false
tags:
  - .Net
  - .Net-Core
  - C#
categories:
  - .Net
  - .Net-Core
  - C#
image:
  filename: ""
  focal_point: Smart
  preview_only: false
---
1. Whenever a method in the .NET framework takes some time, or performs some disk or network IO, always use the asynchronous version of the method you can use instead. eg: use `Task.Delay` instead of `Thread.Sleep`,  `dbContext.SaveChangesAsync` instead of `dbContext.SaveChanges, fileStream.ReadAsync` instead of `fileStream.Read`. *These changes free up the thread-pool threads to do other more useful work, allowing your program to process a higher volume of requests.*
2. *Always use the `async Task` method instead of using `async void` method: If you use the `async void` method, then the caller of the method does not have a way to `await`the outcome of the method.* 
3. *A lot of developers call* asynchronous *methods from* synchronous *methods by using `.Result` or `.Wait` method on the Task. This however ties up the thread , which could be doing more useful work.* \
   Instead of this, whenever you need to call an asynchronous method, you should just make the method you are in asynchronous. 
4. Use the ForEachAsync method with async method, if you need to perform an action on every item in a list. i.e Instead of \
   `customerOrders.ForEach(async c => await SendEmailAsync(c));`\
   do\
   `await customerOrders.ForEachAsync(async c => await SendEmailAsync(c));`

   If we use the first option, then the caller has no way of awaiting it. 
5. Make sure that the application is thread safe. eg: Instead of:

   ```
   public async Task ProcessUserAsync(Guid id, List<User> users)
   {
     var user = await userRepository.GetAsync(id);
     users.Add(user);
   }
   ```

   Do

   ```
   public async Task<User> ProcessUserAsync(Guid id)
   {
     var user = await userRepository.GetAsync(id);
     return user;
   }
   ```

   In the first option, the users list can be modified by multiple threads at the same time.