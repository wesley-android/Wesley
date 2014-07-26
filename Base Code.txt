public class Tracking extends MapActivity implements LocationListener {

LocationManager locman;
LocationListener loclis;
Location Location;
private MapView map;

List<GeoPoint> geoPointsArray = new ArrayList<GeoPoint>();
private MapController controller;
String provider = LocationManager.GPS_PROVIDER;
double lat;
double lon;

@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.map);
    initMapView();
    initMyLocation();
    locman = (LocationManager) getSystemService(Context.LOCATION_SERVICE);
    // locman.requestLocationUpdates(provider,60000, 100,loclis);
    // Location = locman.getLastKnownLocation(provider);

}

/** Find and initialize the map view. */
private void initMapView() {
    map = (MapView) findViewById(R.id.map);
    controller = map.getController();
    map.setSatellite(false);
    map.setBuiltInZoomControls(true);
}

/** Find Current Position on Map. */
private void initMyLocation() {
    final MyLocationOverlay overlay = new MyLocationOverlay(this, map);
    overlay.enableMyLocation();
    overlay.enableCompass(); // does not work in emulator
    overlay.runOnFirstFix(new Runnable() {
        public void run() {
            // Zoom in to current location
            controller.setZoom(24);
            controller.animateTo(overlay.getMyLocation());
        }
    });
    map.getOverlays().add(overlay);
}

@Override
public void onLocationChanged(Location location) {
    if (Location != null) {
        lat = Location.getLatitude();
        lon = Location.getLongitude();
        GeoPoint New_geopoint = new GeoPoint((int) (lat * 1e6),
                (int) (lon * 1e6));
        controller.animateTo(New_geopoint);

    }

}

class MyOverlay extends Overlay {
    public MyOverlay() {
    }

    public void draw(Canvas canvas, MapView mapv, boolean shadow) {
        super.draw(canvas, mapv, shadow);

        Projection projection = map.getProjection();
        Path p = new Path();
        for (int i = 0; i < geoPointsArray.size(); i++) {
            if (i == geoPointsArray.size() - 1) {
                break;
            }
            Point from = new Point();
            Point to = new Point();
            projection.toPixels(geoPointsArray.get(i), from);
            projection.toPixels(geoPointsArray.get(i + 1), to);
            p.moveTo(from.x, from.y);
            p.lineTo(to.x, to.y);
        }
        Paint mPaint = new Paint();
        mPaint.setStyle(Style.STROKE);
        mPaint.setColor(0xFFFF0000);
        mPaint.setAntiAlias(true);
        canvas.drawPath(p, mPaint);
        super.draw(canvas, map, shadow);
    }
}

@Override
public void onProviderDisabled(String provider) {
    // TODO Auto-generated method stub

}

@Override
public void onProviderEnabled(String provider) {
    // TODO Auto-generated method stub

}

@Override
public void onStatusChanged(String provider, int status, Bundle extras) {
    // TODO Auto-generated method stub

}

@Override
protected boolean isRouteDisplayed() {
    // TODO Auto-generated method stub
    return false;
}
}