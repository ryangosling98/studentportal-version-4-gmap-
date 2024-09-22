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
