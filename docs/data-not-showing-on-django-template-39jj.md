# # Django 模板上未显示已解决的数据

> 原文：<https://dev.to/highcenburg/data-not-showing-on-django-template-39jj>

我不知道为什么当我认为我实现正确时，它没有显示在模板上。

模板是:

```
 <div class="row">
              {% for volunteer_name in volunteer %}
                <div class="col-md-4 col-sm-6">
                  <div class="team-wrapper">
                      <div class="team-img">
                          <img src="{{ volunteer_name.img.url }}" class="img-responsive" alt="Image">
                      </div><!-- /.team-img -->

                      <div class="team-title">
                          <h3><a href="#">{{ volunteer_name.name }}</a></h3>
                      </div><!-- /.team-title -->

                  </div><!-- /.team-wrapper -->
              {% endfor %}
                </div><!-- /.col-md-4 -->

              </div><!-- /.row --> 
```

Enter fullscreen mode Exit fullscreen mode

models.py 是:

```
class Volunteer(models.Model):
    image = models.ImageField(max_length=100, upload_to='volunteer/')
    name = models.CharField(max_length=100)
    facebook = models.CharField(max_length=100, validators=[URLValidator])
    twitter = models.CharField(max_length=100, validators=[URLValidator])
    linkedin = models.CharField(max_length=100, validators=[URLValidator])

    def __str__(self):
        return self.name 
```

Enter fullscreen mode Exit fullscreen mode

views.py

```
def home(request):
    amlvideo = AMLVideo.objects.filter().order_by("-category", "-language", "-level")
    volunteer_name = Volunteer.objects.all()
    img = Volunteer.objects.all()
    context = {"home": home}
    return render(request, "aml/home.html", context) 
```

Enter fullscreen mode Exit fullscreen mode

有人能帮我吗？