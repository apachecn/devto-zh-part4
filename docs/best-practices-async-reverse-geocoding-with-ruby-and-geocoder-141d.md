# 最佳实践:使用 Ruby 和 Geocoder 进行异步反向地理编码

> 原文：<https://dev.to/victorhazbun/best-practices-async-reverse-geocoding-with-ruby-and-geocoder-141d>

怎么了伙计们？今天，我将与您分享我如何使用 Geocoder gem 进行反向地理编码。

如你所知，Ruby gem 地理编码器可以让你通过

`reverse_geocoded_by :latitude, :longitude`

在模型中“自动”进行反向地理编码，这很酷，但我发现了一个更好的方法...

首先，Geocoder gem 向 Google maps API 发出一个请求，这需要时间，使得我的响应很慢。因此，在我的记录检测到纬度和经度字段发生变化后，我决定在异步过程中进行反向地理编码。

**app/models/location . Rb**T2】

```
class Location < ApplicationRecord

    ...
    after_commit -> { ReverseGeocodingWorker.perform_async(id) }, if: :coordinates_changed?

    private

    def coordinates_changed?
        latitude_changed? && longitude_changed?
    end
    ...
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们看看负责用 Google Maps API 的结果更新位置记录的`ReverseGeocodingWorker`类

**app/workers/reverse _ geocoding _ worker . Rb**

```
class ReverseGeocodingWorker
  include Sidekiq::Worker
  sidekiq_options retry: true

  def perform(location_id)
    location = Location.find(location_id)
    results = Geocoder.search([location.latitude, location.longitude])
    if results
      result = result.first
      location.update_attributes(
        street_address: result.street_address,
        city: result.city,
        state: result.state,
        postal_code: result.postal_code
      )
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这将加快您的响应时间，因为您将反向地理编码过程委托给了工作人员。

最后，我想分享一个关于地理编码器设置的额外提示，只需启用`cache`选项，就可以增加响应时间并节省您的谷歌地图配额:

**config/initializer/geocoder . Rb**

```
Geocoder.configure(
  # Geocoding options
  lookup: :google,
  ip_lookup: :freegeoip,
  use_https: false,
  cache: Redis.new
) 
```

Enter fullscreen mode Exit fullscreen mode

希望你好运你的反向地理编码功能。

如果你有问题或者想让我写一些关于 Ruby 应用最佳实践的新帖子，请告诉我。

干杯！