---
layout:     post
title:      "My experience with test"
subtitle:   "A path of testing with Rspec"
date:       2017-03-15 21:19:00
author:     "FLemon"
header-img: "img/post-bg-default.jpg"
tags:
    - Rspec
    - TDD
    - vim
    - experience
    - 编程
    - 测试
    - 心德
    - 经验
---

There had been days, weeks, months when I was debugging...testing...and coding...debugging...testing...and coding... and it wasn't long until I find myself keep amending around the same test suit over and over again, after changing codes, or after refactoring. Annoyingly but not surprisingly found that the test itself is very weakly structured/designed.

Big projects with multiple contributors are very hard to maintain a well documented, well styled, and consistent test suits. There are already lots style guides, conventions, tips and so called standards on the internet, that shares the "correct" way of writing tests. To me, those are roads, and sometime, you have to walk out a path that suit you the best. A little metaphors: A common street road from school to home, noisy, busy, but safer as majority of people walk this way, and bright street lights. But you may had your little adventures and stumble into several paths, one may has shorter distance but less lights(unsafe in the dark), one may has longer distance but beautiful sea/country views, one may be about the same distance and through an enjoyable sight seeing spot.

here plays 🎶*[Fleetwood Mac - Go Your Own Way](https://www.youtube.com/watch?v=qNM6IuA87eM)*🎶 in your head

All roads lead to Rome, you see it differently, gain differently, and it is your unique experience. A bit too carry away...Here I shall present you my experience on the little paths I stumble into.

> [changlish] no code, no truth

Right, Rspec, yes, its just a tool, so I won't go into is it better to eat dumpling with chopsitcks or fork. Here's the code:

```ruby
describe "CreateOrUpdateUserService" do
  describe "#initialize" do
    it "requires a key parameter user_name" do
      expect(CreateOrUpdateUSerService.new)
        .to raise_error(ArgumentError, "missing keyword: user_name")
    end

    it "take optional key parameter user_attrs" do
      expect(CreateOrUpdateUserService.new(user_name: "foo"))
        .to be_a(CreateOrUpdateUserService)
      expect(CreateOrUpdateUserService.new(user_name: "foo", user_attrs: { age: 8 }))
        .to be_a(CreateOrUpdateUserService)
    end
  end

  describe "#perform" do
    let(:target_user_name) { "test user name" }
    let(:event_trigger) do
      CreateOrUpdateUserService.new(user_name: target_user_name, user_attrs: user_attrs)
                               .perform
    end

    context "when user already exists" do
      let!(:user) { create(:user, user_name: target_user_name) }

      context "when there is a user profile for this user" do
        let!(:user_profile) { create(:user_profile, user: user, age: 8) }

        context "when new user attribute value is different from that of the existing ones" do
          let(:new_user_id) { "#{user.id}+2" }
          let(:new_age) { user_profile.age+1 }
          let(:user_attrs) { { user_id: new_user_id, age: new_age } }

          it "does not create new user profile" do
            expect { event_trigger }.to change { UserProfile.count }.by(0)
          end

          it "update the updatable user profile attributes" do
            event_trigger
            user_profile.reload

            expect(user_profile.age).to eq(new_age)
            expect(user_profile.user_id).to eq(user.id)
          end
        end

        context "when there is no user profile for this user" do
          it { expect(User.count).to eq(0) }

          it "creates a user profile" do
            expect { event_trigger }.to change { UserProfile.count }.by(1)
          end

          it "saves user attributes to the user profile" do
            event_trigger
            user_profile = UserProfile.last

            expect(user_profile.user_id).to eq user.id
            expect(user_profile.age).to eq new_age
          end
        end
      end

      context "when user does not exists" do
        it { expect(User.count).to eq(0) }

        it "creates a new user" do
          expect { event_trigger }.to change { User.count }.by(1)
        end

        it "creates a new user profile for this user" do
          expect { event_trigger }.to change { UserProfile.count }.by(1)
        end

        it "saves user attributes to the user profile" do
          event_trigger
          user_profile = UserProfile.last

          expect(user_profile.user_id).to eq user.id
          expect(user_profile.age).to eq new_age
        end
      end
    end
  end
end
```

The above code are purely hand drafted, no added artificial...ops, wrong script...
The above code are written from scratch together with this blog, not come from a real project.
You may already have lots questions, and I will try to address them, but first let me guess them.

---

### one expectation per it block?
```ruby
it "take optional key parameter user_attrs" do
  expect(CreateOrUpdateUserService.new(user_name: "foo"))
    .to be_a(CreateOrUpdateUserService)
  expect(CreateOrUpdateUserService.new(user_name: "foo", user_attrs: { age: 8 }))
    .to be_a(CreateOrUpdateUserService)
end
```
well, I find it better with multiple related expectation, yes, as long as they are related
readable enough on the code itself, and flexible to change/extend

---
### undefined variable?
```ruby
describe "#perform" do
  let(:target_user_name) { "test user name" }
  let(:event_trigger) do
    CreateOrUpdateUserService.new(user_name: target_user_name, user_attrs: user_attrs)
                             .perform
  end
```
Ok, you caught me...using undefined variable here...not a big fan? nor me, but there is a benefit here, as every coin has two sides,
here, we don't have much mutation format of this service perform method, doing it this way save space and typing, easier to maintain,
and plus, it will require you to define those undefined variables, good reminder, at least for me.
also am not using subject, I think it is more designed for one-liner it block, i.e. it { is_expected.to be_empty }
I think defining the event_trigger here but not at the top is better as you can see better how you trigger the event

---

### let!?
```ruby
context "when user already exists" do
  let!(:user) { create(:user, user_name: target_user_name) }
```
I like consistency, so like using let/let! pattern over before hook
and here the code fulfils the context description

---
### redundent expectation, totally anti-pattern?
```ruby
context "when there is no user profile for this user" do
  it { expect(User.count).to eq(0) }
```
wow, what's this? lots of you may shake your heads event harder now...
I know, a bit of anti-pattern, as usually there is `DatabaseCleaner` or similar for ensuring this.
But, are you sure? 100% certain? without a doubt guarantee this context says what it says?
I do not, as I've came across multiple times where this below seems redundant test failed the context's expectation.
how? well, if you are using `FactoryGirl`, it is not difficult to have after_create hooks that you maybe using to create
a user_profile with a default user, as it is a mandatory association, and it happened that the new created user has the same
name as your target_user_name...small odd? hehe...[Changlish - I smiled], anyway, I do not want to take my changes, frankly I do
not 100% trust other developers, or even the future me to not accidentaly altered the test setup.
do you recall in the past you have to put a debugger at this stage to check the data setup matches the description?
I remember them...many many times, ok, I am less lucky than you...

---
### more than two level nested contexts?
Yes, I admin it is not very nice to go over two level nested context,
as this can lost developer when they dive too deep and forget about what was the setup several level up.
I tried for sometime restricting myself not to go over two level, and it wasn't the best experience either.
Trying too hard not too go over costs some of the time wasted on restructuring tests, for the sake of not go over two level.
Was it easier to maintain? that depends, some may be readable, but hard to debug or change/extend, some otherwise.
For me, nested context allow me to read better the test setup and the fact that I have a code to fulfil the context, I know better what the setup is like.
I can better/easier to spot the problem, and quite a few times, the test is wrongly setup...causing the codes are wrongly designed...false positive...
Oh by the way, I use vim with folding plugin, which really helps on focus on my nested context without having to jump up the file to see the previous setup
![img](/img/in-post/testing-with-rspec/foldable-vim.png)

---

# tl;dr
Experience makes the unique you, don't blindly follow one single path of truth, there is only single source of truth, but multiple paths lead to it.
Discover your own, and try it out, get a feeling from trying, and you will find it helpful at some point in some situation.
BTW, TDD is not covered in this blog, and it will be later on, but before that, I wish I can help those who don't know how to start, and how to design the app
by start writing a test. The code above is written without designing any real code, purely written as what i thought the service should be doing.
There is a use case of shared example, but I will try to cover that in another blog, so will I cover my experience on writing acceptance tests
