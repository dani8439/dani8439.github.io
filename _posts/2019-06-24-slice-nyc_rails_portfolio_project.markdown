---
layout: post
title:      "Slice-NYC Rails Portfolio Project"
date:       2019-06-24 19:51:53 +0000
permalink:  slice-nyc_rails_portfolio_project
---


Well, this Rails project was certainly a bit of a doozy, but in a good way. 

My goal was to create a Rails application that allowed users to catalogue and rate Pizza Restaurants and Pizza in the Tri-State area. The biggest challenge, and one that was apparent right away, was the sheer scope of such an application. The original models that I had mapped out were not sufficient to display all of the data associated when created something so seemingly simple as a Pie. I hadn't even realized at the start that there are over 20+ different styles of pizza to begin with. Through trial and error, I settled on 13 different models in order to best express the relationships between all the variables, but even then, I know that those 13 different models would not be enough to go forward with another iteration of the project, something I hope to do in the future. 

The models are divided into groups. What's necessary for a Restaurant, and what's necessary for a Pie itself. In the first, there were models for Users, Categories, and Restaurant, with a join table for RestaurantCategories. For Pie itself, there are models for Crusts, Cheeses, Sauces, and Toppings, and join tables for PieCheeses, PieToppings, and PieRestaurants in order to join both Pie and Restaurant together. The final, most important model was RestaurantRatings, where users would ultimately submit a rating for each restaurant on things like service, atmosphere, food, and be able to leave comments on the food itself. In the end, my schema ended up looking like this:

```
ActiveRecord::Schema.define(version: 2019_05_29_174528) do

  create_table "categories", force: :cascade do |t|
    t.string "name"
    t.string "crust"
    t.string "cheese"
    t.string "shape"
    t.string "pan"
    t.string "additional_comments"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "cheeses", force: :cascade do |t|
    t.string "name"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "crusts", force: :cascade do |t|
    t.string "name"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "pie_cheeses", force: :cascade do |t|
    t.integer "pie_id"
    t.integer "cheese_id"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "pie_restaurants", force: :cascade do |t|
    t.integer "pie_id"
    t.integer "restaurant_id"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "pie_toppings", force: :cascade do |t|
    t.integer "pie_id"
    t.integer "topping_id"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "pies", force: :cascade do |t|
    t.string "name"
    t.integer "category_id"
    t.integer "crust_id"
    t.integer "sauce_id"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "restaurant_categories", force: :cascade do |t|
    t.integer "restaurant_id"
    t.integer "category_id"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "restaurant_ratings", force: :cascade do |t|
    t.integer "user_id"
    t.integer "restaurant_id"
    t.integer "atmosphere_score"
    t.integer "service_score"
    t.integer "food_score"
    t.string "comments"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "restaurants", force: :cascade do |t|
    t.string "name"
    t.string "neighborhood"
    t.string "borough"
    t.string "seating"
    t.string "oven"
    t.boolean "multiple_locations", default: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "sauces", force: :cascade do |t|
    t.text "name"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "toppings", force: :cascade do |t|
    t.string "name"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "users", force: :cascade do |t|
    t.string "username"
    t.string "email"
    t.string "password_digest"
    t.string "uid"
    t.string "provider"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

end
```

Pardon the pun, but my initial goal of allowing users to rate *both* pies and restaurants, was biting off a little more than I could chew. In the beginning, it seemed have separate models for Toppings, and Crusts, and Sauces, and even Cheese was a bit too much, but I realized very quickly that it just wasn't possible to create a simple Pie model, that had these things as attributes. Not unless I wanted a database with 4 million lines of repeated code again, and again, and *again.*

Once the models were set up, and proper associations without any typos in place, or mistaken relationships removed that were throwing off the entire thing and not allowing me to save data into the database, it was smooth sailing. I'm kidding. 

One of my favorite things about these portfolio projects is the fact that it's up to our discretion as students to decide how little or how much time to spend working out all the little bits and pieces that make the project work. I am not a super fast coder, as I have realized. I will never have a portfolio project done from start to finish within 5 days. I'm okay with that. Did I take longer on this project than I would have liked, yes. Were there days when it seemed as if I just couldn't write enough code that worked where commiting even one thing was super hard, heck yes. But it's the time spent scrolling through the endless posts that can be found on StackOverflow, or ThoughtBot, or the thousands of posts other coders have written and put up on everything from Medium to their own personal blogs, that is the most illuminating. It's very helpful to understand when you've gotten the same ActiveRecord::AssociationTypeMismatch for the hundredth time, and can't understand why. 

Working with my education coach Aliya, one of the most helpful things during the many weeks of working on SliceNYC, was deciding what my MVP was. My own personal goals, again and again, were always above and beyond what were required by Flatiron itself. Stepping back and reassessing whether or not it was necessary to leave a numbered rating on pie, when it was already possible to do all sorts of ratings for a restaurant was very important. As was filing certain hopes for the project under reach goals, as they fell outside of the project requirements. 

But back to the coding itself. Setting up Omniauth with Facebook was the only thing I felt comfortable doing, as it was the only thing taught in the lessons. There's some sort of security bug going around with those gems itself, and Devise was suggested as an alternative, but trying to follow the setup instructions was very daunting, and very difficult having never been exposed to the process before. I hope Flatiron can put that material back into the curriculum so that future students can learn, as it says right at the very top of the Devise repo on github, that if you've never used it before NOT TO DO SO for a Rails project. That's a big red flag when you're a bit of an anxious coder as I am, and need to be shown the first time or two how to do something before launching into it yourself. Also, Omniauth for Google was very daunting, as I'm not familiar with the Google interface for developers. A lot of the frustration I felt when trying to set up, was just not knowing what to do, and even though there are plenty of posts from other developers about how to go about setting it up, what I found, before giving up in frustration, was out of date and the setup had changed, which all in all made it very frustrating.

However, where would coding be without the frustration? 

A lot of the frustration I found at times with errors in the code, comes down to simple things like typos. Or unnecessary information in say the validations, that was throwing everything off. Other times, because of the model names I chose, there were difficulties in how Rails pluralizes models. I might call a Pie, Pie, but Rails calls it Py. Finding the singular I suppose is difficult, and it was necessary in a lot of the has_many through relationships, to tack on `:source => "your source here in correct singular"`,  in order for the models to work properly, and then of course to remember that it's py and not pie, when writing the proper routes in the controllers so the whole thing doesn't break. For relationships where it's only a `has_many` relationship, the same thing can be achieved by adding a `:class_name` so that it becomes `  has_many :pies, :class_name => 'Pie'` within the model. 

The two biggest challenges, after creating proper models, was the nested resource itself, and scope methods. Thanks God for google. [This](https://backend.turing.io/module2/lessons/nested_resources) post from Turing School of Software helped tremendously, breaking down step by step how to properly set up a nested resource, as well as explaining why certain errors occur. Once I really understood the obvious ordering of things, and always having `@restaurant` and `@restaurant_rating` present in the proper controller, as well as in the view for the magic of `form_for` to use, for my `Restaurants/1/RestaurantRatings/new` nested route, everything clicked into place.

One of my hopes, was for users to be able to filter restaurants by borough. Composing the right queries in Arel was sometimes simple, sometimes quite challenging, but always informative, and actually a bit fun. And of course, once the solution is figured out, it usually seems so glaringly obvious that I'm left to wonder with why it gave me difficulty in the first place. 

Offering users a choice in the restaurants/index.html.erb view using the form_tag, users are presented with this code: 

```
<div class="center-align">
  <h1>Restaurants</h1>

    <h3>Filter Restaurants by Borough:</h3>
    <%= form_tag("/restaurants", method: "get") do %>
      <%= select_tag "borough", options_for_select(["The Bronx", "Brooklyn", "Manhattan", "Queens", "Staten Island", "Other"]), include_blank: true %>
      <%= submit_tag "Filter", class: "button btn-regular" %>
    <% end %>


    <% @restaurants.each do |rest| %>
      <p><%= link_to rest.name, restaurant_path(rest.id) %></p>
    <% end %>
    <br></br>

    <% if logged_in? %>
      <h4><%= link_to "Create a New Restaurant", new_restaurant_path%></h4>
    <% end %>

</div>
```

Which looks like this on your computer: 
![](https://i.imgur.com/kOIzGsth.png)

The code in the restaurants controller for the index route is: 

```
  def index
    @restaurants = Restaurant.all

    if params[:borough] == "The Bronx"
      @restaurants = Restaurant.the_bronx
    elsif params[:borough] == "Brooklyn"
      @restaurants = Restaurant.brooklyn
    elsif params[:borough] == "Manhattan"
      @restaurants = Restaurant.manhattan
    elsif params[:borough] == "Queens"
      @restaurants = Restaurant.queens
    elsif params[:borough] == "Staten Island"
      @restaurants = Restaurant.staten_island
    elsif params[:borough] == "Other"
      @restaurants = Restaurant.other 
    else params[:borough].blank?
      @restaurants = Restaurant.all.sort_by(&:name)
    end
  end
```

And following the correct separation of concerns, the methods defined in the Restaurant Model are: 

``` 
def self.brooklyn
    where(borough: "Brooklyn")
  end

  def self.the_bronx
    where(borough: "The Bronx")
  end

  def self.manhattan
    where(borough: "Manhattan")
  end

  def self.queens
    where(borough: "Queens")
  end

  def self.staten_island
    where(borough: "Staten Island")
  end
```

Using Arel queries to find something simple as a single borough, was clear enough. Composing a query for all restaurants obviously wasn't necessary, as that's something available through the magic of ActiveRecord and Rails. But I had a lot of difficulty trying to define the `self.other` method. I wasn't sure if it was necessary to chain things together, or how to correctly phrase the right piece of information I was after. Thankfully Arel has the `where.not(x)` method available, as [this](https://thoughtbot.com/blog/activerecord-s-where-not-and-nil) post on thoughtbot lays out. The `where.not` method is really useful when wanting to filter out certain information. And creating an array of conditions such as in the example Thoughtbot uses is possible when we're after more than one piece of information: 

```
User.where(subscribed: [nil, false])
```

So that the `def self.other` method can be defined as: 

```
  def self.other
    where.not(borough: ["Brooklyn", "The Bronx", "Manhattan", "Queens", "Staten Island"])
  end
```

And when users choose the "Other" filter from the drop down menu, the results are this: 

![](https://i.imgur.com/1nwS7xmh.png)

It's been a very long road with Slice-NYC, but I'm pretty pleased and pretty happy with where things ended up. Are there things I would change? Of course, there always are. But I feel confident enough in this first iteration that I've laid the groundwork for something that could become even more useful and interesting after completing the remaining sections in the learn curriculum. 

* Bonus tip for anyone who wants to know how to add a favicon or icon to the internet tab of their application. [This](https://stackoverflow.com/questions/4888377/how-to-add-a-browser-tab-icon-favicon-for-a-website) link on Stackoverflow explains how simple it is to add a link in the head of your document layoung to a .png favicon.

