---
layout: post
title: Fetching contacts with CNContactStore
tags: iOS
comments: true
author: eliasz
---
 
Since iOS 9.0 we have a new nice method of fetching and saving contacts - CNContactStore!
Today I will show you how to create a simple UITextField that will be responsible for fetching a contact for you!

Setting up
---
Let me introduce you our textfield! 

![Empty TextField](/images/CNContactStorePost/empty_textfield.png)

Our text field will be unique this time. It will not behave the way it usually does. After tapping it we don't want to see a keyboard, we want to see contacts! In order to achieve this, first we have to implement specific delegate method from our textfield. Inside it, we will open our contact list and tell our textfield not to display a keyboard.

<script src="https://gist.github.com/Eluss/10f6d7eecd8400bcf691.js"></script>

Display contacts
---
Now it's time to present contacts, after creating our `CNContactPickerViewController`, we will assign a delegate for it (we will implement it's method later to catch a contact) and tell it to display contact's phone numbers only.

<script src="https://gist.github.com/Eluss/6c7289307fc8e31f8bb2.js"></script>

![Contact list](/images/CNContactStorePost/selecting_contact.png)

Get a phone number
---
After we choose a phone number from a contact list, we want to receive it in our app. We will do it by catching `CNContactProperty` with `CNContactPickerViewController's` delegate method. You can find many other things when you dig into `CNContactProperty` like name, familyName etc.

<script src="https://gist.github.com/Eluss/b7d9a9525015bb78a1f7.js"></script>

Selected phone number will now appear in our text field ;)

![Filled TextField](/images/CNContactStorePost/filled_textfield.png)

*This article is cross-posted with my [my personal blog](http://eluss.github.io/).*