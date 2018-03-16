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
has_many :todo_lists
---
rake routes
```
![image](https://ws3.sinaimg.cn/large/006tKfTcgy1fpeomsa49qj31dq0ia793.jpg)
