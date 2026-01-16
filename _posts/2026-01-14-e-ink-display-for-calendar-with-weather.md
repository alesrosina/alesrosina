---
tag: 🇬🇧
---

Since I saw an e-ink display, I always thought it would be a cool display for home. Like to show weather, maybe calendar or even some other data like air quality from some sensors, etc. 

At some point few years ago I saw, that Visionect started to sell "Developer Kits", but at a price point I was not willing to pay, since it would be just a hobby project. I was aware of the company since it's beginnings, it's one of a few successful hardware startups from Slovenia in times, when I was also "active" in startup scene. But that might be a write up for some other time. 

So, in last few years I sometimes search the web with "DYI e-ink display" and until recently it was always just too much hustle to set it up. Especially from hardware side, where mostly it was without case and very low level on software side. Until recently, when I found [TRMNL](https://usetrmnl.com/) display. It checked all check boxes for a hobby project - it is reasonably priced (~ 120€) and comes in full package. Meaning you can just take it out of the box, connect to wifi, create account online, select what you want to display and you're good to go. But the last point is the most important: it's open source and you can really tinker with it. You can self host server side and you can also flash it with custom ROM, if you really feel like it.

I went with second option, where I've setup only server side on my home server. It's what they call BYOS (Bring Your Own Server). There are already a few options for servers and after some reading and testing it out, I went for the only version, that (at that point did) support ICS sources aka calendars: [BYOS Laravel](https://github.com/usetrmnl/byos_laravel). Server setup was really straight forward - just use ready docker compose file, and you're ready to go. Linking with the device was also super easy - so all just worked.

![First run with exiting Weather plugin](/assets/images/2026-01-14-e-ink-display-for-calendar-with-weather/first.jpg)

And for the last part of it, I've decided to create my own plugin, that displayed weather & family calendar, so I can hang it in the kitchen to check every morning, what's the weather and if my kid has something special, that I've already forgot about. I'm not going into details of a bit of frustration with limited styling and learning new [styling framework](https://usetrmnl.com/framework/v2_overview) with [templating "langauge"](https://shopify.github.io/liquid/) and all of the limitations that comes with it. But ... that frustration is actually fun part of tinkering! After initial slow start, it went a bit faster and after a few iterations, I was quite happy with a result. It shows calendar items with weather for today in details and quick overview for next 6 days.

![Current version in Slovene](/assets/images/2026-01-14-e-ink-display-for-calendar-with-weather/last.png)

For the weather icons I had to make a mapper and since I didn't want to make too many if-else statements in plugin, I've added one json with icon name & translation to English, German & Slovene. The easiest way to expose it, was to store it on [github as Gist](https://gist.github.com/alesrosina/1f64b7bbfef55961f25bc03b68f34036).

Now, the last bit was to find some good weather source. First thought was looking at the local weather provider [MeteoSchweiz](https://www.meteoschweiz.admin.ch/service-und-publikationen/service/open-data.html), but it's not really easy extractable. So after some search, I've stumbled upon [Open-Meteo service](https://open-meteo.com/en/docs), which has an awesome API and documentation for it.

Without further ado - here's the template code, that you can use, if you are also using same self hosted server. 

> Note, that in "Polling URL" field you need to pass in three data sources, separated by new line:
- your calendar ICS link
- Open-Meteo API link
- above mentioned weather codes json file

So sth like so:
```
<YOUR ICS CALENDAR LINK HERE>
https://api.open-meteo.com/v1/forecast?latitude=<LAT>&longitude=<LONG>&daily=temperature_2m_max,temperature_2m_min,sunrise,sunset,weather_code&hourly=temperature_2m,precipitation_probability,precipitation,weather_code,is_day&current=temperature_2m,weather_code,precipitation,is_day,relative_humidity_2m&timezone=Europe%2FBerlin&forecast_hours=12&past_hours=0&temporal_resolution=hourly_1&forecast_days=6
https://gist.githubusercontent.com/alesrosina/1f64b7bbfef55961f25bc03b68f34036/raw/6c2af56b6e7f9eaa7591ad4410d324350bd13575/weather_codes_bootstrap_icons.json
```



{% raw %}
```html
{% assign weather_type = IDX_2.data | where: "code", IDX_1.current.weather_code | first %}
{% if IDX_1.current.is_day == 1 %}
{% assign weather_type_icon = weather_type.icon %}
{% else %}
{% if weather_type.icon_night %}
{% assign weather_type_icon = weather_type.icon_night %}
{% else %}
{% assign weather_type_icon = weather_type.icon %}
{% endif %}
{% endif %}
<style>
  .icon-big {
    width: 100px;
    display: inline-block;
  }

  .icon-mid {
    width: 32px;
    display: inline-block;
  }

  .icon-small {
    width: 24px;
    display: inline-block;
  }

  .icon-xsmall {
    width: 18px;
    display: inline-block;
  }
</style>
<div class="screen">
  <div class="view view--full">
    <div class="columns">
      <div class="column">
        <div class="title">{{ IDX_1.current.time | l_date: "%A", 'sl' | capitalize }}, {{ IDX_1.current.time | date:
          "%e. %b. %Y" }}</div>
        <div class="grid mt--4">
          <div class="col col--center">
            <div class="grid">
              <div class="row row--center">
                <img class="icon-big"
                  src="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/icons/{{ weather_type_icon }}.svg">
              </div>
            </div>
            <div class="grid mt--4">
              <div class="row row--center">
                <div class="title title--small">{{ weather_type.text_sl }}</div>
              </div>
            </div>
          </div>
          <div class="col col--center">
            <div class="value flex gap--xsmall">
              <img class="icon-mid"
                src="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/icons/thermometer-half.svg">
              <span>{{IDX_1.current.temperature_2m | round}}{{IDX_1.current_units.temperature_2m}}</span>
            </div>
            <div class="value value--xsmall mt--4 flex gap--xsmall">
              <img class="icon-xsmall"
                src="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/icons/thermometer-low.svg">
              <span>{{IDX_1.daily.temperature_2m_min[0] | round}}{{IDX_1.current_units.temperature_2m}}</span>
              <img src="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/icons/thermometer-high.svg"
                class="icon-xsmall ml--2">
              <span>{{IDX_1.daily.temperature_2m_max[0] | round }}{{IDX_1.current_units.temperature_2m}}</span>
            </div>
            <div class="value value--xsmall mt--5 flex gap--xsmall">
              <img class="icon-xsmall" src="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/icons/umbrella.svg">
              <span>{{IDX_1.current.precipitation | round}}{{IDX_1.current_units.precipitation}}</span>
              <img class="icon-xsmall ml--2"
                src="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/icons/moisture.svg">
              <span>{{IDX_1.current.relative_humidity_2m |
                round}}{{IDX_1.current_units.relative_humidity_2m}}</span>
            </div>
          </div>
        </div>
        <div class="grid grid--cols-6 my--4">
          {% for item in IDX_1.hourly.time %}
          {% assign every_second = forloop.index0 | modulo: 2 %}
          {% if every_second == 0 %}
          {% continue %}
          {% endif %}
          {% assign weather_code_item = IDX_2.data | where: "code", IDX_1.hourly.weather_code[forloop.index0] | first %}
          <div class="col">
            <div class="value value--xsmall mb--2">
              <span>{{ item | date: "%H" }}</span>
            </div>
            <div class="mb--2">
              <img class="icon-mid"
                  src="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/icons/{{ weather_code_item.icon }}.svg">
            </div>
            <div class="value value--xxsmall">
              <span>{{ IDX_1.hourly.temperature_2m[forloop.index0] | round }}{{ IDX_1.hourly_units.temperature_2m }}</span>
            </div>
          </div>
          {% endfor %}
        </div>
        {% assign no_events = true %}
        {% for item_cal in IDX_0.ical %}
        {% assign current_date = IDX_1.current.time | date: "%Y-%m-%d" %}
        {% assign cal_date = item_cal.DTSTART | date: "%Y-%m-%d" %}
        {% if current_date == cal_date %}
        {% assign no_events = false %}
        <div class="item">
          <div class="meta"></div>
          <div class="content">
            <div class="title title--small">{{ item_cal.SUMMARY }}</div>
            <span class="label label--small label--underline" data-clamp="1">{% if item_cal.DTSTART contains "T00:00:00"
              and item_cal.DTEND contains "T00:00:00" %}Cel dan{% else %}{{ item_cal.DTSTART | date:
              "%H:%M" }} -
              {{ item_cal.DTEND | date: "%H:%M"
              }}{% endif %}{% if item_cal.LOCATION %} ({{ item_cal.LOCATION }}){% endif %}</span>
          </div>
        </div>
        {% endif %}
        {% endfor %}
        {% if no_events %}
        <div class="item">
          <div class="meta"></div>
          <div class="content">
            <span class="label label--underline">Brez posebnosti</span>
          </div>
        </div>
        {% endif %}
      </div>
      <div class="column">
        {% for item in IDX_1.daily.time %}
        {% if forloop.index0 == 0 %}
        {% continue %}
        {% endif %}
        {% assign weather_code_item = IDX_2.data | where: "code", IDX_1.daily.weather_code[forloop.index0] | first %}
        <div class="mb--2">
          <div class="title mb--2">
            <span>{{ item | l_date: "%A", 'sl' | capitalize }}</span>
            <span class="text--gray-35">, {{ item | date: "%e. %b." }}</span>
          </div>
          <div class="grid grid--cols-3">
            <div class="col col--span-1 col--top">
              <div class="item">
                <div class="content">
                  <div class="value value--xsmall flex">
                    <img class="icon-small"
                      src="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/icons/{{ weather_code_item.icon }}.svg">
                    &nbsp;{{ IDX_1.daily.temperature_2m_min[forloop.index0] | round }}°/{{
                    IDX_1.daily.temperature_2m_max[forloop.index0] | round }}°
                  </div>
                </div>
              </div>
            </div>
            <div class="col--span-2">
              <div class="grid gap">
                <div class="col col--start">
                  {% assign no_events = true %}
                  {% for item_cal in IDX_0.ical %}
                  {% assign current_date = item | date: "%Y-%m-%d" %}
                  {% assign cal_date = item_cal.DTSTART | date: "%Y-%m-%d" %}
                  {% if current_date == cal_date %}
                  {% assign no_events = false %}
                  <div class="item mb--2">
                    <div class="meta"></div>
                    <div class="content">
                      <div class="title title--small">{{ item_cal.SUMMARY }}</div>
                      <span class="label label--small label--underline" data-clamp="1">{% if item_cal.DTSTART contains
                        "T00:00:00" and item_cal.DTEND contains "T00:00:00" %}Cel dan{% else %}{{
                        item_cal.DTSTART | date:
                        "%H:%M" }} -
                        {{ item_cal.DTEND | date: "%H:%M"
                        }}{% endif %}{% if item_cal.LOCATION %} ({{ item_cal.LOCATION }}){% endif %}</span>
                    </div>
                  </div>
                  {% endif %}
                  {% endfor %}
                  {% if no_events %}
                  <div class="item">
                    <div class="meta"></div>
                    <div class="content">
                      <span class="label label--small label--underline">Brez posebnosti</span>
                    </div>
                  </div>
                  {% endif %}
                </div>
              </div>
            </div>
          </div>
        </div>
        {% endfor %}
      </div>
    </div>
  </div>
</div>
```
{% endraw %}

As you can see, I've used Slovene localization, but it would be quite easy to use also English or German. For other languages, you would need to create your own translation file.
