---
layout: post
title: Don’t give up! 🙌 A short history of raising errors
gh-repo: patrykwozinski/patrykwozinski.github.io
gh-badge: [follow]
tags: [php, oop, errors]
comments: true
---

#  Don’t give up! 🙌 A short history of raising errors 
I have written about an error topic about two years ago. From this time some things in my mind have changed and that’s totally fine. I feel I’m just a better engineer from this time.
Okay, so what’s happened to me so I am writing again about the same thread? I want to share with you my observations of how people are working with exceptions — what are the mistakes etc. The examples will be in PHP, but I think you can easily translate the problems into your favorite object-oriented programming language.
I hope you’ll somehow enjoy this post. 🍪

## Should I throw an error every time when something has failed?
A long time ago I would say „yes”, but that was so bad and non-pragmatic. It’s just not that easy. `#itDepends`

From the beginning — what is the exception? It’s an information that something went wrong and we can not process your request/task/something. Sometimes we’re reconciled with the fact that something will not work in the process— it’s obvious! The best example in this case is:
> What if the user’s email is taken?
> Ask him to do an action - type another e-mail or remind a password.

The situation is just an unhappy path so if it’s possible — take control of the user’s flow and rescue situation. Such a situation may occur in the **UserInterface layer**, eg.: while we’re validating input data.
I would not be myself if I didn’t set an example with CQRS. 😄

### CQRS example
- The user is doing a request to your application with an **already taken e-mail address** for registering the account.
- You’re handling requests in **user interface layer** (command-line tool / web controller, etc.).
- In the UI layer, you need to verify if the given data is valid to process a request. So you’re checking if the data like an e-mail address is a valid form (you know: @ etc) and if not — you’re returning a **bad request response** (400) with needed details. That’s **input data requirements validation**.
- The second validation is a **business validation** — so having a fancy CQRS application you’re doing a query to the read model to check if the given e-mail is free in our system. If the e-mail is taken — **don’t throw an error in this step**. It’s a possible situation (just an unhappy path). You should also at this moment return bad request response with details about an already reserved e-mail address.
- If both input data requirements and business validation go well — you can do your write-model operation such as dispatch command or call the application layer service.
- You can/should create a second-level business validation in your write model to make sure you have not encountered an eventual consistency situation with the read model in the UI layer.
- In the write model, you’ll probably assume that the data is correct and validated before. You’re not expecting a business unhappy path. When you encounter eventual consistency and you’re not allowed to process an action — there you can throw an exception with more business-context details. **It’s too late to control the user’s flow** and ask for another e-mail, etc. Following our example - raise a similar error to `UserEmailAlreadyTaken`. You can use a static factory method to add more context information like `public static function withEmail(Email $email)`.
- In the next steps, of course, **you can control the unhappy path** and for example: send e-mail to the user with the info that someone has tried to register or his e-mail. It’s up to you and up to your business model. 💸

I hope everything is clear. If you have any questions — feel free to ask me on LinkedIn/Twitter priv message — I’d be happy to help you. 👌

## Standard language exceptions library
In our programming languages, we have many predefined exceptions. I’d like to rest on **PHP SPL exceptions**, but I think you can somehow move the examples into your sandbox.

### When to use SPL exceptions
If you’re working on something close to the infrastructure layers. Eg. you assume the loading YAML configuration would not ever fail. BUT! In some special day, you lost permissions in your container or there was another weird situation — you just can’t do some infrastructure operation, you can not control the flow. That’s the time when you’ll probably throw an exception related to the given problem. And I think it may be some `RuntimeException` because it would not be handled (except logging, etc). So… in this situation, I think creating a custom exception for something like `CanNotLoadSomeWeirdYamlConfigurationException` does not make sense. Unless in the case where infrastructure is a crucial part of your business. throw new RuntimeException(‘Can not load some weird config’); is just enough. 😉

### Catching general exceptions is just a bad habit
I have written and spoken on some meetups about it, but it’s okay to repeat the sentence. **We should not catch general SPL exceptions for handling business logic**. It’s better to create dedicated exceptions for such situations. If you would catch something like RuntimeException or worse Exception (but not for logging and other infrastructural operations) go and burn in hell, seriously. **If you don’t know what are you catching — just don’t do that**.
Also, you don’t need to add information to the docblock about that the method can throw some SPL runtime exception because… yes — you shouldn’t catch it.

---

Exceptions are not always as simple as those we learn in college. It’s often a complicated topic, but it’s worth getting it to avoid doing a code-design fails in the future. I hope you had a good time reading this article and you have learned something that could be useful for you in the near work.

**Go ahead, let’s chat!**
- [Twitter](https://twitter.com/patrykwozinski)
- [LinkedIn](https://www.linkedin.com/in/patrykwozinski)
