# Get a list of all the filters

## Explain

+ 因為 controller 常透過繼承的方式，導致 filter 的順序，常常需要看很多的檔案才能知道
+ 透過 `_process_action_callbacks` 可以得到 before、after 與 around filter 的順序


## Example

+ 得到 ApplicationController 的 before、after 與 around method filter 順序的 list

rails console（rails 3）
 ```ruby
ApplicationController._process_action_callbacks.find_all{|x| x.kind == :before}.map(&:filter)
ApplicationController._process_action_callbacks.find_all{|x| x.kind == :after}.map(&:filter)
ApplicationController._process_action_callbacks.find_all{|x| x.kind == :around}.map(&:filter)
 ```

 rails console（rails 4）
 ```ruby
ApplicationController._process_action_callbacks.find_all{|x| x.kind == :before}.map(&:name)
ApplicationController._process_action_callbacks.find_all{|x| x.kind == :after}.map(&:name)
ApplicationController._process_action_callbacks.find_all{|x| x.kind == :around}.map(&:name)
 ```
