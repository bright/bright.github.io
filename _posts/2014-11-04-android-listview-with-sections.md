---
layout: post
excerpt: "ListViewWithSectionsAdapter for Android ListView, simple way to implement list view with sections."
title: ListViewWithSectionsAdapter for Android ListView, simple way to implement list view with sections.
modified: 2014-11-04
tags: [android]
comments: true
author: daniel
---

If you need to implement Android ListView with sections like in iOS world then the code below can help you with that. The first thing you need is to implement two View providers, one for section view and second for section entry view. To do that you just have to create a class that implements ListViewWithSectionsAdapter.ViewProvider<T> interface, example below.

{% highlight Java %}

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
{% endhighlight %}

Another thing you have to implement is DataProvider:

{% highlight Java %}
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
{% endhighlight %}

Then you need to create an instance of ListViewWithSectionsAdapter class and assing that to ListView as adapter:
{% highlight Java %}
listView.setAdapter(new ListViewWithSectionsAdapter(getActivity(), dataProvider, new ProfileSectionViewProvider(), new ProfileEntryViewProvider()));
{% endhighlight %}

That's all. 

The implementation of ListViewWithSectionsAdapter you can find on github: [ListViewWithSectionsAdapter for Android ListView](https://gist.github.com/danielmakurat/2ed33ef7a3d1100149ef)
