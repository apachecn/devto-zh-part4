# 你能告诉我为什么同一个月显示两次相同的数据吗？

> 原文：<https://dev.to/tipu/can-you-please-tell-me-why-same-month-showing-twice-with-same-data-5hif>

你能告诉我为什么同一个月用同样的数据显示两次吗？
车型. py

```
class Event(models.Model):
    title = models.CharField(max_length=255, default=None, unique=True)
    link = models.URLField(default=None, null=True)
    description = models.TextField()
    date = models.DateField(default=None, null=True)
    is_published = models.BooleanField(default=False)
    created = models.DateTimeField(auto_now_add=True)
    updated = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.title

    class Meta:
        db_table = 'events'
        verbose_name = 'event'
        verbose_name_plural = 'events'

class EventGroup(models.Model):
    event = models.ForeignKey(Event, related_name='events', on_delete=models.CASCADE)
    date = models.DateField()
    created = models.DateTimeField(auto_now_add=True)
    updated = models.DateTimeField(auto_now=True)
    class Meta:
        db_table = 'event_group' 
```

Enter fullscreen mode Exit fullscreen mode

serializer.py

```
class EventGroupSerializer(serializers.ModelSerializer):
    events = serializers.SerializerMethodField(method_name='get_events')
    month_year = serializers.SerializerMethodField(method_name='get_month_year')

    total = serializers.SerializerMethodField(method_name='get_count_events')
    class Meta:
        model = EventGroup
        fields = ('month_year', 'total', 'events',)

    def get_count_events(self, instance):
        org_date = str(instance['date'])
        date = datetime.datetime.strptime(org_date, "%Y-%m-%d")
        month = date.month
        year = date.year
        count = Event.objects.filter(date__month=month, date__year=year).count()
        return count

    def get_month_year(self, instance):
        org_date = str(instance['date'])
        date = datetime.datetime.strptime(org_date, "%Y-%m-%d").date()
        month_year = str(date.month)+'-'+str(date.year)
        return str(month_year)

    def get_events(self, instance):
        org_date = str(instance['date'])
        date = datetime.datetime.strptime(org_date, "%Y-%m-%d")
        month = date.month
        year = date.year
        events = Event.objects.filter(date__month=month, date__year=year)
        event_serializer = EventSerializer(events, many=True)
        return event_serializer.data 
```

Enter fullscreen mode Exit fullscreen mode

views.py

```
class EventGroupData(ListAPIView):
    queryset = EventGroup.objects.all()

    serializer_class = EventGroupSerializer

    def get_queryset(self):
        queryset = self.queryset

        return queryset.annotate(month=TruncMonth('date')).values('date').annotate(c=Count('id')).order_by() 
```

Enter fullscreen mode Exit fullscreen mode

如果我将 queryset 改为 query set . annotate(month = TruncMonth(' date '))。值(“月”)。批注(c=Count('id '))。order_by()...它显示带有“日期”的关键错误。
结果

```
{
    "count": 4,
    "next": null,
    "previous": null,
    "results": [
        {
            "month_year": "6-2019",
            "events": [
                {
                    "id": 30,
                    "title": "Event 2",
                    "description": "Event 2",
                    "date": "2019-06-02"
                },
                {
                    "id": 31,
                    "title": "Event 3",
                    "description": "Event 3",
                    "date": "2019-06-04"
                }
            ]
        },
        {
            "month_year": "6-2019",
            "events": [
                {
                    "id": 30,
                    "title": "Event 2",
                    "description": "Event 2",
                    "date": "2019-06-02"
                },
                {
                    "id": 31,
                    "title": "Event 3",
                    "description": "Event 3",
                    "date": "2019-06-04"
                }
            ]
        },
        {
            "month_year": "9-2019",
            "events": [
                {
                    "id": 32,
                    "title": "Event 4",
                    "description": "Event 4",
                    "date": "2019-09-04"
                },
                {
                    "id": 29,
                    "title": "Event 1",
                    "description": "Event 1",
                    "date": "2019-09-25"
                }
            ]
        },
        {
            "month_year": "9-2019",
            "events": [
                {
                    "id": 32,
                    "title": "Event 4",
                    "description": "Event 4",
                    "date": "2019-09-04"
                },
                {
                    "id": 29,
                    "title": "Event 1",
                    "description": "Event 1",
                    "date": "2019-09-25"
                }
            ]
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

应该是这样的

```
{
    "count": 4,
    "next": null,
    "previous": null,
    "results": [
        {
            "month_year": "6-2019",
            "events": [
                {
                    "id": 30,
                    "title": "Event 2",
                    "description": "Event 2",
                    "date": "2019-06-02"
                },
                {
                    "id": 31,
                    "title": "Event 3",
                    "description": "Event 3",
                    "date": "2019-06-04"
                }
            ]
        },
        {
            "month_year": "9-2019",
            "events": [
                {
                    "id": 32,
                    "title": "Event 4",
                    "description": "Event 4",
                    "date": "2019-09-04"
                },
                {
                    "id": 29,
                    "title": "Event 1",
                    "description": "Event 1",
                    "date": "2019-09-25"
                }
            ]
        },

    ]
} 
```

Enter fullscreen mode Exit fullscreen mode