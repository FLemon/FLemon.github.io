---
layout:     post
title:      "Development Optimisation"
subtitle:   "Optimise in a way you may not thought of"
date:       2017-04-06 22:00:00
author:     "FLemon"
header-img: "img/post-bg-default.jpg"
tags:
    - Development
    - Optimisation
    - 编程
    - 心德
    - 优化
---

1. you may be aware that: `String + String` is slower than `String.concat(String)`, as `+` copies the memory of each `String` and create a third `String`, while `#concat` just append the original `String`. However you don't want to `#concat` your `String` if you need the original `String` to be used somewhere else. To get the best illustration purpose from this, please pretend you don't know  `frozen_string_literal`

2. you may also know the famous N+1 query problem: `User.includes(:posts).all.map { |user| user.posts }` instead of `User.all.map { |user| user.posts }` - as the former performs only two queries, while the latter performs N+1 queries, N=User.count. However, you need to apply the includes where sensible, or else you will have unused eager load problem.

3. you may had the experience of moving a model away from its Single Table Inheritance structure. Or you are moving towards it, I am sure you have a good reason of doing either. I think a good design should evolve with the business.

4. you may already using some sort of background job to process complicated tasks/algorithms to smooth the user experience. However, when immediate result is more desired, you probably want to turn that off.

5. you may feel worth spending money on 3rd party online/cloud services to replace a big piece of functionality of the app. Why do everything yourself while others can do it better for you. Sorry, I didn't know you are superman, in that case, yes, you have a long time to live, and you can do everything better than the others

6. you may tried to argue over using some technology stack is better than the other. I know a true fact, a real KongFu master can win a fight with just a tree branch against knifes and blades. On that, I think tools are just tools, what good do they do if they are not being used right.

7. you may heard of all kinds of business analysis tools(i.e. google analytics) that is suppose to help to improve business value. People are trying hard to make a software/app/system that satisfy all possible audiences, while forgot to listen to the ones that really matters, I think Analytics are good way to learn how your users are using your software/app/system, like point 6, it is just a tool for the user, you need to know whether they are not using it right, or the tool is not the right tool for them.

8. you may be struggling from the current business model, not offering the right service, or not targeting the right customers, or just want to improve the channel of delivering value to customer to improve profit. Business model is an important blueprint to the success, I think, it is like the skeleton of a strong muscled body with strong mind, without the strong muscle and strong mind, the body is not good enough, but to standout, you need all three, yet it is hard to build muscle and strong mind on a weak or wrongly grown skeleton(well, there seems to be always some exceptions for any thing).

---

Above list briefly described layers of a normal/simple business development, these layers wraps together like an onion, starting from top is the most inner layer of the onion(Yes, I am sure there are more other layers not included here...please take them as an abstracted representatives). Outside layer is where the customer can see and touch, then decide wether to purchase it and take it home, so it make sense to make sure the outside layer is done well.
It shall attract more new customers.

Then when the onion is being put on the dinning table, customer is going to taste the inner layer of the onion, if badly grown, it ruin the whole dish, or even the whole dinning experience. new customer become last customer. Or if the inner layer is grown well, adults on the dinning table loves it, but the 3 children on the other side of the table hates it, which may leads to a difficult bed time experience. This new customer become rare customer, there are still chance when all children are away to in-laws, and the customer happen to think of the onion again.

What I was trying to say is...well, I am not trying to make a point here, rather, I am just trying to share you my thoughts and open a conversation to talk about it. Please feel free to comment about it, after all, what do I know, I am just a developer.

Anyway, to carry on...I want to provide you the ultimate solution for solving the above onion selling problem, by waiting for a wise guy to put it into this blog comment. Ok, I am stalling your page visit time here, which makes my tracking analysis report look nice.

I can see the above onion selling problem everywhere in our daily life, in different form, if you would have the habit of review what you've done in the day before the day finishes, you will probably had this thought "I could had done that...", or "I need to do that instead of this...". Sounds familiar? You could have this kind of thoughts immediate after you've just done something. Everyday you are seeking different ways of doing something, that you think is better. Is it better, or worse? The same way may impact differently to different individuals. Why bother convincing others, that a way is better than another way. Here comes the music 🎶*[Marty Robbins - One Man's Trash Is Another Man's Treasure](https://www.youtube.com/watch?v=eSHP_seFnDA)*🎶

Enough philosophy talk...In case you can't relate those above paragraphs with the list at the beginning. I shall try to draw lines to link them. ... ... Sorry, I can't draw anything in this blog, technically I haven't find a way to do it yet. Let me write more...
When you are focusing so much on one line of code's performance, or a service's architecture design, stop for a while and think: given the current circumstance, is it worth the effort NOW? Is it ok to comeback later and refine it? Is it something your client will appreciate right away...etc? Or in another way round, when you are exciting about your fantasy thought of a business model, before spending money on it, think about feasibility, technology wise, geography wise, lawful wise...etc.

Finally, If you are managing a normal local small restaurant, be ready to serve customers that come in and order a jacket potato with BEANs and (mayo)TUNAs, and be happy to do so.
