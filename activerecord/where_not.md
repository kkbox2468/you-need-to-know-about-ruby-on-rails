# where.not

## Explain

+ rails 4.0 support
+ User.where.not(name: 'Joe') 等價於 User.where('name != ?', 'Joe')
+ not null 的實作
  + rails 3.0 - User.where('name IS NOT NULL') 
  + rails 4.0 - User.where.not(name: nil)
