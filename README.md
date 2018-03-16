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
```
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
