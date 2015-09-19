# validates_with

## Explain

+ 透過自定義的 class 來做驗證，但必須繼承於 ActiveModel::Validator
+ 自定義的 class 必須實作 validate(record) 方法
+ 驗證錯誤訊息必須加入到 record.errors[自定義名稱]

## Eaample

+ 目的：將免洗 email 排除，不可用於 user 註冊
+ disposable-email-domains.json：內容請參考 [ivolo/disposable-email-domains](https://github.com/ivolo/disposable-email-domains/blob/master/index.json)


/app/models/user.rb
```ruby
class User < ActiveRecord::Base
  validates_with EmailDomainValidator
end
```

/lib/validators/email_domain_validator.rb
```ruby
class EmailDomainValidator < ActiveModel::Validator

  DISPOSABLE_DOMAINS =
    JSON.parse(File.read("#{Rails.root}/config/disposable-email-domains.json")) rescue []

  def validate(user)
    error_message = I18n.t 'devise.registrations.messages.invalid_email'

    begin
      domain_name = Mail::Address.new(user.email).domain
    rescue
      user.errors[:email] << error_message
    end

    if DISPOSABLE_DOMAINS.include?(domain_name)
      user.errors[:email] << error_message
    end
  end
end
```
