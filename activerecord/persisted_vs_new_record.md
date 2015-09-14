# persisted? vs new_record?

## Explain

	- persisted? 判斷是否已經存入 database 且不是新增的資料
	- new_record? 判斷是否是新增資料

## Eaample

```ruby
user = User.new

user.new_record?  # true
user.persisted?   # false

user = User.first

user.new_record?  # false
user.persisted?   # true
```
