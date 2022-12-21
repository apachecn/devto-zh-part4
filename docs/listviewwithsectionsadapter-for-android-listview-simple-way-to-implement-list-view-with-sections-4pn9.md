# Android ListView 的 ListViewWithSectionsAdapter，实现带有节的列表视图的简单方法。

> 原文：<https://dev.to/brightdevs/listviewwithsectionsadapter-for-android-listview-simple-way-to-implement-list-view-with-sections-4pn9>

如果你需要在 Android ListView 中实现像 iOS world 这样的部分，那么下面的代码可以帮助你。您首先需要实现两个视图提供者，一个用于截面视图，另一个用于截面条目视图。为此，您只需创建一个实现 ListViewWithSectionsAdapter 的类。ViewProvider 接口，示例如下。

```
 public class ProfileSectionViewProvider implements ListViewWithSectionsAdapter.ViewProvider<BasicUserProfileSection> {

@Override
public View getView(int position, View convertView, ViewGroup parent, BasicUserProfileSection item, Context context) {
    View v = convertView;
    if (v == null) {
        LayoutInflater inflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        v = inflater.inflate(R.layout.basic_user_profile_section, null);
    }
    TextView sectionTitleTextView = (TextView) v.findViewById(R.id.sectionTitle);
    sectionTitleTextView.setText(context.getString(item.getDisplayNameResourceId()));
    return v;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你必须实现的另一件事是 DataProvider:

```
ListViewWithSectionsAdapter.DataProvider dataProvider = new ListViewWithSectionsAdapter.DataProvider() {
    @Override
    public List<Object> getSections() {
        ArrayList<Object> objects = new ArrayList<Object>();
        objects.addAll(BasicUserProfile.getDefault().getSections());
        return objects;
    }

    @Override
        public List<Object> getEntriesForSection(Object section) {
            ArrayList<Object> entries = new ArrayList<Object>();
            entries.addAll(((BasicUserProfileSection) section).getEntries());
            return entries;
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

然后您需要创建一个 ListViewWithSectionsAdapter 类的实例，并将其作为适配器分配给 ListView:

```
listView.setAdapter(new ListViewWithSectionsAdapter(getActivity(), dataProvider, new ProfileSectionViewProvider(), new ProfileEntryViewProvider())); 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。

github 上可以找到的 listviewwithsectionadapter 的实现:【Android ListView 的 listviewwithsectionadapter