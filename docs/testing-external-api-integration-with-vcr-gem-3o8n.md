# 测试外部 API 与 VCR gem 的集成

> 原文：<https://dev.to/spitsman/testing-external-api-integration-with-vcr-gem-3o8n>

假设您的应用程序通过 API 连接到一个外部服务，并且您有一个处理和解析响应的 API 包装器。 [VCR gem](https://github.com/vcr/vcr) 让您能够以特殊格式(`cassetes`)存储解析的响应。VCR 在第一次测试运行时向 API 发出一个真正的请求，并在下一次测试运行时将它的响应写到 cassete。

首先你需要设置录像机的配置:

```
VCR.configure do |config|
  config.cassette_library_dir = "spec/vcr_cassettes"
  config.hook_into :webmock
end 
```

然后编写如下规格:

```
RSpec.describe ExternalService do
  describe '#new_order' do
    let(:params) { { foo: 'bar' } }

    it 'creates new order' do
      VCR.use_cassette("external_service") do
        result = subject.new_order(params) # makes HTTP POST to an external service

        expect(result.successful?).to be_truthy
        expect(result.data).to have_key('order_id')
      end
    end
  end
end 
```

更多详情见创业板官方页面[。](https://github.com/vcr/vcr)