# 用 RSpec 测试 GraphQL-Ruby 突变

> 原文：<https://dev.to/rjrobinson/testing-graphql-ruby-mutations-with-rspec-3ngc>

来自中爱开发者的 xpost 到这么多，也想在这里分享一下。

首先，我想知道我有多喜欢使用 GraphQL。大约一年前，我开始修改它，后来完成了一个重写的项目，结果令人惊讶。然而，抛开利弊不谈，我需要一种测试方法。对你来说，这可能不是最好的解决方案，但对我的团队和我们的用例来说，这是行之有效的。

测试需要涵盖几个方面。用户有访问权限吗？我们使用自己的专家黑客。是否发送了正确的参数？而回应是否如预期的那样回来了？这听起来像是一个基本的控制器测试，所以从那时起，我就这样对待它。

通常，在一个 RSpec 控制器测试中，我们有一个#get 或#post 方法，允许我们与那个端点交互，我想继续这个模式。这些通常会返回一个#response 对象，包含我们想要测试的所有内容。

```
module GraphQL::TestHelpers

  attr_accessor :gql_response

  # The returned results of a GraphQL query
  # @return [data] this is the bulk of the return to test.
  # @return [error] any time a query, mutation, subscription throws and error
  class GQLResponse
    attr_reader :data, :errors

    def initialize(args)
      @data = args['data'] || nil
      @errors = args['errors'] || nil
    end
  end

  # basic query to interact with the GraphQL API.
  # @param [query] required The query string that would be passed to the schema.
  def query(query, variables: {}, context: {})

    converted = variables.deep_transform_keys! {|key| key.to_s.camelize(:lower)} || {}

    res = MyRailsAppSchema.execute(query, variables: converted, context: context, operation_name: nil)
    @gql_response = GQLResponse.new(res.to_h)
  end

  alias_method :mutation, :query
end 
```

一旦我定义了#query 和#mutation 方法，传入参数就很容易了。

```
mutation  createBook($bookInput:  BookInputType!){  createBook(bookInput:  $bookInput)  {  book  {  title  author  pages  }  }  }  #Arguments==  {  bookInputType:  {  title:  "Testing  GraphQL-Ruby  Mutations  With  RSpec",  author:  "me",  pages:  1  }  } 
```

假设我们有一个简单的图书应用程序，我们想创建一本新书。下面是测试，虽然这看起来很基本，但我希望它足够清晰，可以理解背后的想法。

```
require 'rails_helper'
require_relative '../test_helpers'
include GraphQL::TestHelpers

describe 'CreateBook', type: :mutation do
  describe 'Creating a Book' do

    let(:user) {build_stubbed(:user)}
    let(:mutation_type) {"createBook"}
    let(:mutation_string) {<<- GQL
      mutation createBook($bookInput: BookInputType!){
       createBook(bookInput: $bookInput) {
          book {
            title
            author
            pages
          }
        }
      }
    GQL
    }

    context 'when a user has all the required permissions and parameters' do

      before do
        mutation mutation_string,
                 variables: {
                    bookInputType: {
                      title: "Testing GraphQL-Ruby Mutations With RSpec",
                      author: "me", 
                      pages: 1
                    }
                  }
                 },
                 context: {current_user: user}
      end

      it 'should return no errors' do
        expect(gql_response.errors).to be_nil
      end

      it 'should return the book object' do
        expect(gql_response.data[mutation_type]["book"]).to include("title" => "Testing GraphQL-Ruby Mutations With RSpec")
      end
    end

    context 'when a user has not passed required parameters parameters' do

      before do
        mutation mutation_string,
                 variables: {
                    bookInputType: {
                      title: "Testing GraphQL-Ruby Mutations With RSpec",
                    }
                  }
                 },
                 context: {current_user: user}
      end

      it 'should return errors' do
        expect(gql_response.errors).to be_truthy
      end

    end
  end
end 
```

为什么我决定这样做？几个原因。第一，我想像对待其他 post 请求一样对待这个突变。数据进去，数据出来。我看到很多测试试图做一些疯狂的事情，只是为了击中变异内部的分解器。我觉得没必要。使用 GraphQL 模式的现有接口应该可以测试您需要什么。希望这有所帮助，我如何你提供一些反馈。我想看看其他人是如何测试的。

可以在推特上找到我。:) [@rjrobinson](https://dev.to/rjrobinson)