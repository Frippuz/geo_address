# geo_address
A plugin to automatically lookup address information using device_tracker position.

The plugins will create new state information named after the existing device_trackers in Homeassistant.

The plugin uses device trackers<br/>
<img src="docs/device_trackers.png" alt="Device trackers"/>

...to create address info<br/>
<img src="docs/geo_address.png" alt="geo_address"/>


The plugins uses Nominatim from geopy.geocoder as the base for the reverse lookups.


## Usage

### Copy files
Create a folder named `geo_address` in custom_components. Copy __ _init_ __.py and manifest.json to that folder.

### Set up configuration.yaml

Add this to `configuration.yaml`

```yaml
geo_address:
  use_state: true
  use_timed: true  
  update_interval: 60
  update_period: 300
  fields_display: road city county  
  exclude:
    entities:
      - device_tracker.name_name 
  include:
    entities:
      - device_tracker.another_device
```

| Property | Optional | Comment 
| -------- | -------- | ------
| use_timed | `true` |  use timed updates.
| use_state | `true` |  use state change updates.
| update_interval | `true` | how often to check for new position.
| update_period | `true` | for how long back in time to check for changes.
| fields_display | `true` |  what fields to display (see "fields options" below for options).
| exclude | `true` | see below.
| include | `true` | see below.

Both `use_state` and `use_timed` are optional, but one should be selected to be able to update values.

### Include or exclude
```yaml
exclude:
  entities:
    - device_tracker.name_name
   
include:
  entities:
    - device_tracker.another_device
```     

You can either `include` the devices you want to track or `exclude` the ones you do not want to track. Only `entities` makes sense here.
If you would enter the same device in both, it won't be tracked.
### Fields settings

Default fields_display value: `road village city county state postcode country`

It's hard to make a list of what to find here when the options are limitless.
There are the common ones `road, house_number, neighbourhood, suburb, village, city, city_district, county, state_district, state, postcode, country, country_code`<br/>
But you might find more like `hotel, cafe, pedestrian` ... 

If both city and county are added and the value for the both fields are the same only `city` is displayed.

## Implementation
Example implementation in Lovelace. I've used Thomas Lovéns Lovelace module <a href="https://github.com/thomasloven/lovelace-markdown-mod">lovelace-markdown-mod</a> for this. 
```yaml
type: markdown
  content: >
    <h3>Adress</h3>
    [[ geo_address.fredric_fredric.attributes.road ]] [[ if(geo_address.fredric_fredric.attributes.house_number != undefined, geo_address.fredric_fredric.attributes.house_number, "") ]]
    [[ geo_address.fredric_fredric.attributes.city ]] 
```

<img src="docs/address_card.png" alt="Address card"/>

## Example usage
<img src="docs/example.png" alt="Fredric address"/>
Standard picture entity card and markdown below. 

```yaml
    - type: "custom:vertical-stack-in-card"
      cards:
      - type: vertical-stack
        cards:
          - type: picture-entity
            entity: device_tracker.fredric_fredric
            image: /local/lovelace/Fredric.jpg
            show_name: false
          - type: markdown
            content: >
              <h3>Adress</h3>
              [[ geo_address.fredric_fredric.attributes.road ]] [[ if(geo_address.fredric_fredric.attributes.house_number != undefined, geo_address.fredric_fredric.attributes.house_number, "") ]]
              [[ if(geo_address.fredric_fredric.attributes.city != undefined, geo_address.fredric_fredric.attributes.city, "") ]]
              [[ if(geo_address.fredric_fredric.attributes.village != undefined, geo_address.fredric_fredric.attributes.village, "") ]]
              [[ if(geo_address.fredric_fredric.attributes.county != geo_address.fredric_fredric.attributes.city, geo_address.fredric_fredric.attributes.county, "") ]]  
```

In the screen dump above. I've used <a href="https://github.com/custom-cards/vertical-stack-in-card">vertical-stack-in-card</a> to be able to make a tighter fit between the the cards.

## Right now
The are issues left to figure out, like implementing a better way to do state change tracking. The current way isn't good enough and timed updates is probably not the best way to go around this problem. 

Drop a line if you have suggestions or found bugs :-)