# Live Project Python w/Django Framework

## Introduction

This was a live project at the Tech Academy where I worked on a team for two weeks developing a web application utilizing Django Framework. We were given abstractions to create functionality
for our personal applications and were required to research how to do these on our own. My app focused on Reflective Journaling. I created a database
to help track moods, sleep patterns, and express thoughts and feelings through journaling for self discovery. The user was able create, edit, and delete entries and well track their mood over time.
 I also included an API that provided a daily inspirational quotes along with a search feature for specific topics. This was a great experiencing learning Django Framework, CRUD functionality, 
API's, and working in a devs ops team environment. PyCharm was used at the IDE with Azure Devops and Agile/Scrum methodologies to develop this project.

## Creating a Model and Form

I created a entry model and defined its characteristics: name, text, date posted, mood, and hour slepts. I used a tuple for mood and hours sleep since method was more app
friendly and made tracking that data easier. To obtain the user input I created a form using Bulma CSS framework then created a views function to render my display page.

<h3>Model</h3>

```

MOOD_CHOICES = [
    ('Awesome', 'Awesome'),
    ('Good', 'Good'),
    ('Meh', 'Meh'),
    ('Down', 'Down'),
    ('Awful', 'Awful'),
]

SLEEP_CHOICES = [
    ('0-4 Hours','0-4 Hours'),
    ('5-6 Hours','5-6 Hours'),
    ('7-8 Hours','7-8 Hours'),
    ('9-10 Hours','9-10 Hours'),
    ('11-24 Hours','11-24 Hours'),
]

class Entry(models.Model):
    name = models.CharField(max_length=50, default='', blank=False, null=True)
    text = models.TextField()
    date_posted = models.DateTimeField(auto_now_add=True)
    Mood = models.CharField(max_length=50, default='', choices=MOOD_CHOICES, blank=False, null=True)
    Hours_Slept = models.CharField(max_length=50, default='', choices=SLEEP_CHOICES, blank=False, null=True)



    def __str__(self):
        return 'Entry #{}'.format(self.id)

    class Meta:
        verbose_name_plural = 'Entries'

```

<h3>Form</h3> 

```

class EntryForm(ModelForm):
    class Meta:
        model = Entry
        fields = '__all__'

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.fields['text'].widget.attrs.update({'class' : 'textarea','placeholder' : 'what\'s on your mind?'})
        self.fields['name'].widget.attrs.update({'class': 'input'})
        self.fields['Mood'].widget.attrs.update({'class': 'input'})
        self.fields['Hours_Slept'].widget.attrs.update({'class': 'input'})

```

<h3>Form View</h3> 

```

# Main display page
def Display(request):

    entries = Entry.objects.order_by('-date_posted')

    context = {'entries': entries}

    return render(request, 'Entries/Display.html', context)

```

## Displaying Index, Create, Edit, and Delete Functions.

Templates were created for each these individual pages to render the entry data and provide user with CRUD functionality.


```

{% extends 'Entries/Base.html' %}

{% block content%}


    <section class="section">
        <div class="container">

            <article class="media">
                <div class="media-content">
                    <div class="content">
                        <a href="{% url 'EntryPage' %}">Add new entry</a>
                    </div>

                </div>
            </article>

            {% for entry in entries %}  <!-- # Loops over each item in an array, making the item available in a context variable. -->

                <article class="media">
                        <div class="media-content">
                            <div class="content">
                                <p>

                                    <strong><a href="{% url 'DetailsPage' pk=entry.id %}">{{ entry.date_posted }}</a></strong>
                                    <br> <strong> My mood was: </strong> {{ entry.Mood }}
                                    <br> <strong>Hours Slept: </strong> {{ entry.Hours_Slept }}
                                    <br> <strong>Passing Thoughts: </strong> {{ entry.text }}
                                    <br> <strong>Posted By:</strong> {{ entry.name | lower|capfirst }}
                                </p>
                            </div>
                        </div>
                </article>
            {% endfor %}
        </div>
    </section>





{% endblock %}

```

## API

I added an API that provided a daily random advice in JSON format ,  I also included a search feature to API to render advice based on the users input.

```

API1  ( this API returns a single random slip of advice )
  def quotes(request):
    response = requests.get('https://api.adviceslip.com/advice')
    print(response.status_code)
    quotedata = response.json()

    return render(request, 'Entries/API.html', {
        'slip': quotedata['slip'],

    })

API2 ( search functionality )
def quotes(request):
    search_result = {}
    if 'x' in request.GET:
        x = request.GET['x']
        url = 'https://api.adviceslip.com/advice/search/%s' % x
        response = requests.get(url)
        search_was_successful = (response.status_code == 200)
        search_result = response.json()
        search_result = search_result['slips']
        text = json.dumps(search_result, sort_keys=True, indent=4)
        print(text)  

    return render(request, 'Entries/API.html', {'search_result': search_result})

```


## Final take away and other skills learned

This project was fun, engaging, and provided great real world experience. Through doing this I was able to: 
* Increase my knowledgebase of programming languages and concepts used.
* Working with a team to complete tasks in a timely and efficient manner
* Learn new efficiencies from other developers by observing their workflow and asking questions.
* Focus on writing clean efficient code.




