# 才华横溢 todo 教学案例 6

# 第1部分 新建专案
```
cd workspace
rails new todo
cd todo
rails s
```

```
atom .
```

```
git status
git add .
git commit -m "initial commit"
git remote add origin https://github.com/shenzhoudance/todo.git
git push -u origin master
```
![image](https://ws3.sinaimg.cn/large/006tKfTcly1fpenkmb3k0j31kw0x51kx.jpg)

```
git checkout -b todo_list
rails g scaffold todo_list title:string description:text
rake db:migrate
rails server
http://localhost:3000/todo_lists
```
![image](https://ws4.sinaimg.cn/large/006tKfTcly1fpenqtvi0hj31as0fot9q.jpg)

```
config/routes.rb
root 'todo_lists#index'
http://localhost:3000/
```
![image](https://ws1.sinaimg.cn/large/006tKfTcgy1fpenuyvgcij30w80f0jsc.jpg)

```
rails g model todo_item content:string todo_list:references
rake db:migrate
```
![image](https://ws1.sinaimg.cn/large/006tKfTcgy1fpeo1es9dcj31e40jagpw.jpg)

```
config/routes.rb
---
resources :todo_lists do
  resources :todo_items
end
---
app/models/todo_list.rb
---
has_many :todo_items
---
rake routes
```
![image](https://ws3.sinaimg.cn/large/006tKfTcgy1fpeomsa49qj31dq0ia793.jpg)




```
rails g controller todo_items

class TodoItemsController < ApplicationController
  def create
    @todo_list = TodoList.find(params[:todo_list_id])
    @todo_ltem = @todo_list.todo_items.create(params[:todo_item].permit(:content))
  end

  private
    def set_todo_list
      @todo_list = TodoList.find(params[:todo_list_id])
  end

    def todo_item_params
      params[:todo_item].permit(:content)
  end

end

class TodoItemsController < ApplicationController
  before_action :set_todo_list

  def create
    @todo_item = @todo_list.todo_items.create(todo_item_params)
    redirect_to @todo_list
  end

  private
    def set_todo_list
      @todo_list = TodoList.find(params[:todo_list_id])
  end

    def todo_item_params
      params[:todo_item].permit(:content)
  end

end
```
![image](https://ws3.sinaimg.cn/large/006tKfTcgy1fpeqwt5novj30ro0bw753.jpg)

```
app/views/todo_items/_todo_item.html.erb
---
<p><%= todo_item.content %></p>
<%= link_to "Delete",todo_list_todo_item_path(@todo_list,todo_item.id),methed: :delete,data:{ confirm:"are you sure?"} %>
---
```
```
app/controllers/todo_items_controller.rb
---
class TodoItemsController < ApplicationController
  before_action :set_todo_list

  def create
    @todo_item = @todo_list.todo_items.create(todo_item_params)
    redirect_to @todo_list
  end

  def destory
    @todo_item = @todo_list.todo_items.find(params[:id])
    if @todo_item.destroy
      flash[:success] = "todo list item was delete."
    else
      lash[:error] = "todo list item could not be delete."
    end
    redirect_to @todo_list
  end

  private
    def set_todo_list
      @todo_list = TodoList.find(params[:todo_list_id])
  end

    def todo_item_params
      params[:todo_item].permit(:content)
  end

end
---
```
```
git status
git add .
git commit -m "Add Todo Lists & Todo Items"
git push origin todo_list
```

```
git checkout -b add_completed_at
rails g migration add_completed_at_to_todo_items completed_at:datetime
rake db:migrate
```
![image](https://ws4.sinaimg.cn/large/006tKfTcgy1fpes13zukjj31kw0h2n19.jpg)

```
config/routes.rb
---
Rails.application.routes.draw do
  resources :todo_lists do
    resources :todo_items do
      member do
        patch :complete
      end
    end
  end
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
  root 'todo_lists#index'
end
```
```
rake routes
```
![image](https://ws4.sinaimg.cn/large/006tKfTcgy1fpesavqcu9j31hu0j6jwn.jpg)

```
app/views/todo_items/_todo_item.html.erb
---
<p><%= todo_item.content %></p>
<%= link_to "Delete",todo_list_todo_item_path(@todo_list,todo_item.id),methed: :delete,data:{ confirm:"are you sure?"} %>

<%= link_to "mark as complete",complete_todo_list_todo_item_path(@todo_list,todo_item.id) ,method: :patch %>
```
![image](https://ws4.sinaimg.cn/large/006tKfTcgy1fpesfb8zztj30xw0nkgnd.jpg)

```
app/controllers/todo_items_controller.rb
---
class TodoItemsController < ApplicationController
  before_action :set_todo_list
  before_action :set_todo_item, except:[:create]

  def create
    @todo_item = @todo_list.todo_items.create(todo_item_params)
    redirect_to @todo_list
  end

  def destory
    if @todo_item.destroy
      flash[:success] = "todo list item was delete."
    else
      lash[:error] = "todo list item could not be delete."
    end
    redirect_to @todo_list
  end

  def complete
    @todo_item.update_attribute(:completed_at,Time.now)
    redirect_to @todo_list,notice:"todo item  complete"
end

  private
    def set_todo_list
      @todo_list = TodoList.find(params[:todo_list_id])
  end

    def set_todo_item
      @todo_item = @todo_list.todo_items.find(params[:id])
    end

    def todo_item_params
      params[:todo_item].permit(:content)
  end

end

```
![image](https://ws3.sinaimg.cn/large/006tKfTcgy1fpesnob9rvj314y0oq76c.jpg)

```
app/views/todo_items/_todo_item.html.erb
---
<div class="row clearfix">
  <% if todo_item.completed? %>
  <div class="complete">
    <%= link_to "mark as complete",complete_todo_list_todo_item_path(@todo_list,todo_item.id) ,method: :patch %>
  </div>

  <div class="todo_item">
    <p style="opacity: 0.4;"><strike><%= todo_item.content %></strike></p>
  </div>

  <div class="trash">
   <%= link_to "Delete",todo_list_todo_item_path(@todo_list,todo_item.id),methed: :delete,data:{ confirm:"are you sure?"} %>
  </div>
  <% else %>
  <div class="complete">
    <%= link_to "mark as complete",complete_todo_list_todo_item_path(@todo_list,todo_item.id) ,method: :patch %>
  </div>

  <div class="todo_item">
    <p><<%= todo_item.content %>></p>
  </div>

  <div class="trash">
   <%= link_to "Delete",todo_list_todo_item_path(@todo_list,todo_item.id),methed: :delete,data:{ confirm:"are you sure?"} %>
  </div>
  <% end %>
</div>
```
```
app/models/todo_item.rb
---
class TodoItem < ApplicationRecord
  belongs_to :todo_list

  def completed?
    !completed_at.blank?
  end
end
```
![image](https://ws1.sinaimg.cn/large/006tKfTcgy1fpet7wvh36j314o0xcmzr.jpg)

```
git status
git add .
git commit -m "mark as complete ability"
git push origin add_completed_at
```
```
git checkout -b add_scss
app/assets/stylesheets/application.css.scss
---
$white_opaque: rgba(250, 250, 250, .3);
$dark: #1F7972;

* {
 box-sizing: border-box;
}
html {
 height: 100%;
}

body {
 height: 100%;
 background: -webkit-linear-gradient(40deg, #4CB8C4 10%, #EAECC6 100%);
 background:    -moz-linear-gradient(40deg, #4CB8C4 10%, #EAECC6 100%);
 background:     -ms-linear-gradient(40deg, #4CB8C4 10%, #EAECC6 100%);
 background:      -o-linear-gradient(40deg, #4CB8C4 10%, #EAECC6 100%);
 background:         linear-gradient(40deg, #4CB8C4 10%, #EAECC6 100%);
 font-family: 'Lato', sans-serif;
}

.clearfix:before,
.clearfix:after {
   content: " ";
   display: table;
}

.clearfix:after {
   clear: both;
}

#notice {
 text-align: center;
 font-size: 0.6em;
 color: $dark;
 font-weight: 100;
}

.container {
 width: 50%;
 max-width: 750px;
 background: $white_opaque;
 margin: 3em auto 0 auto;
 border-radius: 7px;
 -webkit-box-shadow: 0 0 4px 3px rgba(0,0,0,.3);
 box-shadow: 0 0 4px 4px rgba(0,0,0,.03);
 padding: 1em 0;
}

.todo_list_title {
 text-align: center;
 font-weight: 700;
 font-size: 2.5rem;
 text-transform: uppercase;
 color: white;
 margin: 0;
 a {
   text-decoration: none;
   color: white;
   transition: all .4s ease-in-out;
   &:hover {
     opacity: 0.4;
   }
 }
}

.todo_list_sub_title {
 margin: 0 0 3em 0;
 text-align: center;
 font-size: 0.9em;
 color: $dark;
 font-weight: 100;
}

.index_row {
 padding: 2em;
 border-bottom: 1px solid rgba(250,250,250, .3);
 .todo_list_sub_title {
   margin-bottom: 0;
 }
}

#todo_items_wrapper {
 .row {
   width: 100%;
   border-top: 1px solid rgba(250,250,250, .3);
   border-bottom: 1px solid rgba(250,250,250, .3);
   .complete {
     width: 10%;
     float: left;
     text-align: center;
     border-right: 1px solid rgba(250,250,250, .3);
     padding: 1em 0;
   }
   .todo_item {
     width: 80%;
     float: left;
     p {
       margin: 0;
       padding: 1em;
       color: $dark;
       font-weight: 100;
     }
   }
   .trash {
     width: 10%;
     float: left;
     text-align: center;
     border-left: 1px solid rgba(250,250,250, .3);
     padding: 1em 0;
   }
   i {
     color: white;
     transition: all .4s ease-in-out;
     &:hover {
       color: $dark;
     }
   }
 }
 #form {
   margin-top: 2em;
   padding: 0 5%;
   input[type="text"] {
     width: 72%;
     margin-right: 2%;
     display: inline-block;
     outline: none;
     background: rgba(250,250,250,.4);
     border: none;
     height: 40px;
     border-radius: 4px;
     padding: 1em 2em;
   }
   input[type="submit"] {
     background: rgba(250,250,250,.4);
     outline: none;
     border: none;
     height: 40px;
     border-radius: 4px;
     width: 25%;
     display: inline-block;
     transition: all .4s ease-in-out;
     cursor: pointer;
     &:hover {
       background: $dark;
     }
   }
   ::-webkit-input-placeholder { color: $dark; }
 }
}

.links {
 display: block;
 margin: 1.5em auto 0 auto;
 text-align: center;
 font-size: 0.8em;
 color: white;
 a {
   color: white;
 }
}

.forms {
 padding: 0 5%;
}
label {
 color: $dark;
}
input[type="text"], textarea {
 width: 100%;
 margin: .5em 2% 1em 0;
 display: inline-block;
 outline: none;
 background: rgba(250,250,250,.4);
 border: none;
 height: 40px;
 border-radius: 4px;
 padding: 1em 2em;
}
textarea {
 height: 200px;
}
input[type="submit"] {
 background: white;
 outline: none;
 border: none;
 height: 40px;
 border-radius: 4px;
 width: 25%;
 display: inline-block;
 transition: all .4s ease-in-out;
 cursor: pointer;
 color: $dark;
 &:hover {
   background: $dark;
   color: white;
 }
}
::-webkit-input-placeholder { color: $dark; }
```
![image](https://ws1.sinaimg.cn/large/006tKfTcgy1fpeti55ij9j31kw0q6b29.jpg)
```
app/views/layouts/application.html.erb
---
<body>
  <div class="container">
    <%= yield %>
  </div>
</body>
```
![image](https://ws4.sinaimg.cn/large/006tKfTcgy1fpetlmzu4vj31kw10db29.jpg)

```
app/views/todo_lists/index.html.erb
---
<% @todo_lists.each do |todo_list| %>
  <div class="index_row clearfix">
    <h2 class="todo_list_title"><%= link_to todo_list.title, todo_list %></h2>
    <p class="todo_list_sub_title"><%= todo_list.description %></p>
  </div>
<% end %>

<div class="links">
  <%= link_to "New Todo List", new_todo_list_path %>
</div>
```
![image](https://ws3.sinaimg.cn/large/006tKfTcgy1fpetq0gjaoj31c00sckdn.jpg)
```
app/views/layouts/application.html.erb
https://fonts.google.com/
https://fontawesome.com/how-to-use/svg-with-js
---
<head>
  <title>才华横溢</title>
  <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track' => true %>
  <%= javascript_include_tag 'application', 'data-turbolinks-track' => true %>
  <%= csrf_meta_tags %>
  <link href='http://fonts.googleapis.com/css?family=Lato:300,400,700' rel='stylesheet' type='text/css'>
  <link href="//maxcdn.bootstrapcdn.com/font-awesome/4.2.0/css/font-awesome.min.css" rel="stylesheet">
</head>
```

```
app/views/todo_items/_todo_item.html.erb
---
<div class="complete">
  <%= link_to complete_todo_list_todo_item_path(@todo_list, todo_item.id), method: :patch do %>
          <i style="opacity: 0.4;" class="fa fa-check"></i>
        <% end %>
</div>
```
![image](https://ws2.sinaimg.cn/large/006tKfTcgy1fpeuem76lkj31560yank7.jpg)
```
<div class="row clearfix">
  <% if todo_item.completed? %>
  <div class="complete">
    <%= link_to complete_todo_list_todo_item_path(@todo_list, todo_item.id), method: :patch do %>
    				<i style="opacity: 0.4;" class="fa fa-check"></i>
    			<% end %>
  </div>

  <div class="todo_item">
    <p style="opacity: 0.4;"><strike><%= todo_item.content %></strike></p>
  </div>

  <div class="trash">
   <%= link_to "Delete",todo_list_todo_item_path(@todo_list,todo_item.id),methed: :delete,data:{ confirm:"are you sure?"} %>
  </div>
  <% else %>
  <div class="complete">
    <%= link_to "mark as complete",complete_todo_list_todo_item_path(@todo_list,todo_item.id) ,method: :patch %>
  </div>

  <div class="todo_item">
    <p><<%= todo_item.content %>></p>
  </div>

  <div class="trash">
   <%= link_to "Delete",todo_list_todo_item_path(@todo_list,todo_item.id),methed: :delete,data:{ confirm:"are you sure?"} %>
  </div>
  <% end %>
</div>
---
<div class="row clearfix">
	<% if todo_item.completed? %>
		<div class="complete">
			<%= link_to complete_todo_list_todo_item_path(@todo_list, todo_item.id), method: :patch do %>
				<i style="opacity: 0.4;" class="fa fa-check"></i>
			<% end %>
		</div>
		<div class="todo_item">
			<p style="opacity: 0.4;"><strike><%= todo_item.content %></strike></p>
		</div>
		<div class="trash">
			<%= link_to todo_list_todo_item_path(@todo_list, todo_item.id), method: :delete, data: { confirm: "Are you sure?" } do %>
				<i class="fa fa-trash"></i>
			<% end %>
		</div>
	<% else %>
		<div class="complete">
			<%= link_to complete_todo_list_todo_item_path(@todo_list, todo_item.id), method: :patch do %>
				<i class="fa fa-check"></i>
			<% end %>
		</div>
		<div class="todo_item">
			<p><%= todo_item.content %></p>
		</div>
		<div class="trash">
			<%= link_to todo_list_todo_item_path(@todo_list, todo_item.id), method: :delete, data: { confirm: "Are you sure?" } do %>
				<i class="fa fa-trash"></i>
			<% end %>
		</div>
	<% end %>
</div>
```
![image](https://ws2.sinaimg.cn/large/006tKfTcgy1fpeuh19tr3j313o0vgatk.jpg)
```
app/views/todo_lists/show.html.erb
---<p id="notice"><%= notice %></p>

<p>
  <strong>Title:</strong>
  <%= @todo_list.title %>
</p>

<p>
  <strong>Description:</strong>
  <%= @todo_list.description %>
</p>

<div id="todo_items_wrapper">
  <%= render @todo_list.todo_items %>
  <div id="form">
  <%= render "todo_items/form" %>
  </div>
<div>

<%= link_to 'Edit', edit_todo_list_path(@todo_list) %> |
<%= link_to 'Back', todo_lists_path %>
---
<p id="notice"><%= notice %></p>

<h2 class="todo_list_title"><%= @todo_list.title %></h2>
<p class="todo_list_sub_title"><%= @todo_list.description %></p>

<div id="todo_items_wrapper">
	<%= render @todo_list.todo_items %>
	<div id="form">
		<%= render "todo_items/form" %>
	</div>
</div>

<div class="links">
	<%= link_to 'Edit', edit_todo_list_path(@todo_list) %> |
	<%= link_to 'Delete', todo_list_path(@todo_list), method: :delete, data: { confirm: "Are you sure?" }
 %> |
  <%= link_to 'Back', todo_lists_path %>
</div>
```
![image](https://ws2.sinaimg.cn/large/006tKfTcgy1fpeukhsgxlj314g0xgqnw.jpg)

```
app/views/todo_lists/new.html.erb
---
<h1>New Todo List</h1>

<%= render 'form', todo_list: @todo_list %>

<%= link_to 'Back', todo_lists_path %>
---
<h1 class="todo_list_title">New Todo List</h1>
<div class="forms">
  <%= render 'form', todo_list: @todo_list %>
</div>

<div class="links">
  <%= link_to 'Back', todo_lists_path %>
</div>

```
![image](https://ws3.sinaimg.cn/large/006tKfTcgy1fpeuxlrggij315o0rwqjl.jpg)

```
<h1>Editing Todo List</h1>

<%= render 'form', todo_list: @todo_list %>

<%= link_to 'Show', @todo_list %> |
<%= link_to 'Back', todo_lists_path %>
---

<h1 class="todo_list_title">Editing Todo List</h1>
<div class="forms">
  <%= render 'form', todo_list: @todo_list %>
</div>

<div class="links">
  <%= link_to 'Show', @todo_list %> |
  <%= link_to 'Back', todo_lists_path %>
</div>
```
![image](https://ws4.sinaimg.cn/large/006tKfTcgy1fpev02fmh9j313o0r8h0s.jpg)
