# studentportal-version-4-gmap-


here in addision to  previous versions gmap is added in profile page 

# Step 1: Update the profile template

1. Update portal/templates/portal/profile.html:
```html
{% extends 'portal/base.html' %}

{% block content %}
<h2>Student Profile</h2>
<p>Username: {{ profile.user.username }}</p>
<p>Student ID: {{ profile.student_id }}</p>
<p>Major: {{ profile.major }}</p>
<p>GPA: {{ profile.gpa }}</p>

<h3>Search Location</h3>
<input id="pac-input" type="text" placeholder="Enter a location">
<div id="map" style="height: 400px; width: 100%;"></div>

<h3>Recent Notifications</h3>
{% for notification in notifications %}
    <div class="notification">
        {{ notification.message }}
        <small>({{ notification.created_at|date:"F d, Y H:i" }})</small>
    </div>
{% empty %}
    <p>No notifications.</p>
{% endfor %}

<script src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&libraries=places"></script>
<script>
function initMap() {
    var map = new google.maps.Map(document.getElementById('map'), {
        center: {lat: 9.6838, lng: 76.3354}, // Coordinates for Government Polytechnic College, Cherthala
        zoom: 13
    });

    var input = document.getElementById('pac-input');
    var searchBox = new google.maps.places.SearchBox(input);
    map.controls[google.maps.ControlPosition.TOP_LEFT].push(input);

    map.addListener('bounds_changed', function() {
        searchBox.setBounds(map.getBounds());
    });

    var markers = [];
    searchBox.addListener('places_changed', function() {
        var places = searchBox.getPlaces();

        if (places.length == 0) {
            return;
        }

        markers.forEach(function(marker) {
            marker.setMap(null);
        });
        markers = [];

        var bounds = new google.maps.LatLngBounds();
        places.forEach(function(place) {
            if (!place.geometry) {
                console.log("Returned place contains no geometry");
                return;
            }
            var icon = {
                url: place.icon,
                size: new google.maps.Size(71, 71),
                origin: new google.maps.Point(0, 0),
                anchor: new google.maps.Point(17, 34),
                scaledSize: new google.maps.Size(25, 25)
            };

            markers.push(new google.maps.Marker({
                map: map,
                icon: icon,
                title: place.name,
                position: place.geometry.location
            }));

            if (place.geometry.viewport) {
                bounds.union(place.geometry.viewport);
            } else {
                bounds.extend(place.geometry.location);
            }
        });
        map.fitBounds(bounds);
    });
}
</script>
<script async defer
    src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&libraries=places&callback=initMap">
</script>
{% endblock %}
```
Certainly, I'll explain this code for you. This appears to be a Django template for a student profile page that includes a Google Maps integration. Let's break it down section by section:



1. Template Structure:
   ```django
   {% extends 'portal/base.html' %}
   {% block content %}
   ...
   {% endblock %}
   ```
   This template extends a base template (`portal/base.html`) and defines content for the "content" block.

2. Student Profile Information:
   ```django
   <h2>Student Profile</h2>
   <p>Username: {{ profile.user.username }}</p>
   <p>Student ID: {{ profile.student_id }}</p>
   <p>Major: {{ profile.major }}</p>
   <p>GPA: {{ profile.gpa }}</p>
   ```
   This section displays the student's profile information, accessing data from a `profile` object.

3. Google Maps Integration:
   ```html
   <h3>Search Location</h3>
   <input id="pac-input" type="text" placeholder="Enter a location">
   <div id="map" style="height: 400px; width: 100%;"></div>
   ```
   This creates an input field for location search and a div for the Google Map.

4. Recent Notifications:
   ```django
   <h3>Recent Notifications</h3>
   {% for notification in notifications %}
       <div class="notification">
           {{ notification.message }}
           <small>({{ notification.created_at|date:"F d, Y H:i" }})</small>
       </div>
   {% empty %}
       <p>No notifications.</p>
   {% endfor %}
   ```
   This section loops through recent notifications, displaying the message and timestamp. If there are no notifications, it shows "No notifications."

5. Google Maps JavaScript:
   ```html
   <script src="https://maps.googleapis.com/maps/api/js?key=AIzaSyDhA5izpxDOsgemjBXpms0PZXV59KTQadA"></script>
   <script>
   function initMap() {
       // ... (Map initialization code)
   }
   </script>
   ```
   This script initializes the Google Map. It sets the initial center to the coordinates of Government Polytechnic College, Cherthala, and sets up a search box for location searching.

6. Places Search Functionality:
   The JavaScript code sets up event listeners for the search box, allowing users to search for places. When a place is selected, it adds a marker to the map and adjusts the map bounds to show the selected location.

7. Google Maps API Loading:
   ```html
   <script async defer
       src="https://maps.googleapis.com/maps/api/js?key=AIzaSyDhA5izpxDOsgemjBXpms0PZXV59KTQadA&libraries=places&callback=initMap">
   </script>
   ```
   This script tag loads the Google Maps JavaScript API with the Places library and calls the `initMap` function when loaded.

Important notes:
- The Google Maps API key is exposed in the HTML. In a production environment, you should secure this key to prevent unauthorized use.
- The template assumes that `profile` and `notifications` objects are passed from the view to the template.
- The map is centered on a specific location (Government Polytechnic College, Cherthala) by default.

This template creates a page that displays a student's profile information, recent notifications, and a Google Map that allows location searching. It's likely part of a larger student portal or educational management system.

Would you like me to elaborate on any specific part of this code?


# Step 2: Update the base template

2. Update portal/templates/portal/base.html to remove the inline styles and add a link to a separate CSS file:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>StudentPortal</title>
    <link rel="stylesheet" href="{% static 'css/style.css' %}">
</head>
<body>
    <!-- Rest of the content remains the same -->
</body>
</html>
```

# Step 3: Create a CSS file

3. Create a new file: portal/static/css/style.css
```css
body { 
    font-family: Arial, sans-serif; 
    line-height: 1.6; 
    margin: 0; 
    padding: 20px; 
}
nav { 
    background: #333; 
    padding: 10px; 
}
nav a { 
    color: #fff; 
    text-decoration: none; 
    padding: 10px; 
}
.container { 
    max-width: 800px; 
    margin: auto; 
    padding: 20px; 
}
.notification { 
    background: #f4f4f4; 
    padding: 10px; 
    margin-bottom: 10px; 
    border-left: 4px solid #333; 
}
.messages { 
    padding: 10px; 
    margin-bottom: 20px; 
}
.alert-success { 
    background-color: #d4edda; 
    border-color: #c3e6cb; 
    color: #155724; 
}
.alert-info { 
    background-color: #d1ecf1; 
    border-color: #bee5eb; 
    color: #0c5460; 
}
.alert-warning { 
    background-color: #fff3cd; 
    border-color: #ffeeba; 
    color: #856404; 
}
.alert-danger { 
    background-color: #f8d7da; 
    border-color: #f5c6cb; 
    color: #721c24; 
}
#map {
    height: 400px;
    width: 100%;
    margin-top: 20px;
}
#pac-input {
    background-color: #fff;
    font-family: Roboto;
    font-size: 15px;
    font-weight: 300;
    margin-left: 12px;
    padding: 0 11px 0 13px;
    text-overflow: ellipsis;
    width: 300px;
}
```

# Step 4: Update settings.py

4. Ensure that 'django.contrib.staticfiles' is in your INSTALLED_APPS in StudentPortal/settings.py:
```python
INSTALLED_APPS = [
    # ...
    'django.contrib.staticfiles',
    # ...
]

# At the end of the file, add:
STATIC_URL = '/static/'
```

# Step 5: Update views.py

5. The profile view in portal/views.py remains the same:
```python
@login_required
def profile(request):
    student_profile = StudentProfile.objects.get(user=request.user)
    notifications = Notification.objects.filter(user=request.user).order_by('-created_at')[:5]
    return render(request, 'portal/profile.html', {'profile': student_profile, 'notifications': notifications})
```
