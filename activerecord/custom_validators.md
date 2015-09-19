# custom validators

## Explain

+ 透過自訂的 class 做驗證，但必須繼承於 ActiveModel::EachValidator
+ 自定義的 class 必須實作 validate_each(record, attribute, value) 方法
+ 驗證錯誤訊息必須加入到 record.errors[自定義名稱]

## Example

+ 目的：將免洗 email 排除，不可用於 user 註冊
+ disposable-email-domains.json：內容請參考 [ivolo/disposable-email-domains](https://github.com/ivolo/disposable-email-domains/blob/master/index.json)
+ delegate 用法：將 get_mail_domain 與 valid? 指向給 self，意思是當呼叫 get_mail_domain 與 valid?，實際上是呼叫 self.get_mail_domain 與 self.valid?

#### path： /app/models/user.rb

```ruby
class User < ActiveRecord::Base
  validates :email, ban_disposable: true
end
```
#### path： /lib/validators/ban_disposable_validator.rb

```ruby
class BanDisposableValidator < ActiveModel::EachValidator

  DISPOSABLE_DOMAINS =
    JSON.parse(File.read("#{Rails.root}/config/disposable-email-domains.json")) rescue []

  delegate :get_mail_domain, :valid?, to: self

  def validate_each(record, attribute, value)
    error_message = I18n.t 'devise.registrations.messages.invalid_email'
    unless valid?(value)
      record.errors[:email] << error_message
    end
  end

  def self.valid?(mail)
    domain_name = get_mail_domain(mail)
    return false if domain_name.nil?
    !DISPOSABLE_DOMAINS.include?(domain_name)
  end

  private

  def self.get_mail_domain(mail)
    Mail::Address.new(mail).domain rescue nil
  end
end
```
