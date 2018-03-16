# 才华横溢movie_review教学案例 6

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
git push origin todo_list
```
