package ca.uwaterloo.lab4_205_10;

import java.util.Arrays;

import mapper.MapLoader;
import mapper.MapView;
import mapper.NavigationalMap;
import android.app.Activity;
import android.app.Fragment;
import android.hardware.Sensor;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
import android.os.Bundle;
import android.view.ContextMenu;
import android.view.ContextMenu.ContextMenuInfo;
import android.view.LayoutInflater;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
//import android.widget.ImageView;
import android.widget.LinearLayout;
import android.widget.TextView;

public class MainActivity extends Activity {
	// Global graph object initiation
	static LineGraphView accelGraph;
	// Static variable for the 4 sensor
	// Needed to call the resetMax() function
	static mySensorEventListener s2;
	static LinePlotter l1;

	// Import map
	static MapView mapview;
	static NavigationalMap map;

	@Override
	public void onCreateContextMenu(ContextMenu menu, View v,
			ContextMenuInfo menuInfo) {
		super.onCreateContextMenu(menu, v, menuInfo);
		mapview.onCreateContextMenu(menu, v, menuInfo);
	}

	@Override
	public boolean onContextItemSelected(MenuItem item) {
		return super.onContextItemSelected(item)
				|| mapview.onContextItemSelected(item);
	}

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		if (savedInstanceState == null) {
			accelGraph = new LineGraphView(getApplicationContext(), 100, Arrays.asList("x", "y", "z"));

			mapview = new MapView(getApplicationContext(), 1000,
					1000, 40, 40);
			map = MapLoader.loadMap(getExternalFilesDir(null),
					"E2-3344-Lab-room-S15-tweaked.svg");
			mapview.setMap(map);
		
			//Declare position listener
			l1 = new LinePlotter(map);
			
			//Add it to mapview so it can use it on its event change.
			mapview.addListener(l1);
			
			//Register context menu (select start and end menu)
			registerForContextMenu(mapview);			 
			getFragmentManager().beginTransaction()
					.add(R.id.container, new PlaceholderFragment()).commit();
		}
	}

	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		// Inflate the menu; this adds items to the action bar if it is present.
		getMenuInflater().inflate(R.menu.main, menu);
		return true;
	}

	@Override
	
	public boolean onOptionsItemSelected(MenuItem item) {
		// Handle action bar item clicks here. The action bar will
		// automatically handle clicks on the Home/Up button, so long
		// as you specify a parent activity in AndroidManifest.xml.
		int id = item.getItemId();
		if (id == R.id.action_settings) {
			return true;
		}
		return super.onOptionsItemSelected(item);
	}

	/**
	 * A placeholder fragment containing a simple view.
	 */
	public static class PlaceholderFragment extends Fragment {
		public PlaceholderFragment() {
		}

		@Override
		public View onCreateView(LayoutInflater inflater, ViewGroup container,
				Bundle savedInstanceState) {
			
			
		
			//-----------------
			

			View rootView = inflater.inflate(R.layout.fragment_main, container,
					false);

			TextView tv = (TextView) rootView.findViewById(R.id.label1);
			tv.setText("Lab 3 - Sensors");

			TextView tv1 = new TextView(rootView.getContext());
			tv1.setText("Graph");

			TextView rotation = new TextView(rootView.getContext());

			LinearLayout lineLay = (LinearLayout) rootView
					.findViewById(R.id.label2);
			lineLay.setOrientation(LinearLayout.VERTICAL);
			lineLay.addView(tv1);

			// Graph- note: getApplicationContext() comes from Activity; it does
			// not
			// exist within Fragment.
			

			// Setup compass animation
			// float currentDeg = 0f;
			// RotateAnimation animation = new RotateAnimation(
			// currentDeg,
			// -s2.getRotation(),
			// Animation.RELATIVE_TO_SELF, 0.5f,
			// Animation.RELATIVE_TO_SELF,
			// 0.5f);
			//
			// animation.setDuration(250);
			// animation.setFillAfter(true);
			//
			// compass.startAnimation(animation);
			// currentDeg = -s2.getRotation();
			//
			
			
			// Request sensor manage
			SensorManager sensorManager = (SensorManager) rootView.getContext()
					.getSystemService(SENSOR_SERVICE);
			// Request sensor type
			Sensor accel = sensorManager
					.getDefaultSensor(Sensor.TYPE_LINEAR_ACCELERATION);
			Sensor accel2 = sensorManager
					.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
			Sensor magnet = sensorManager
					.getDefaultSensor(Sensor.TYPE_MAGNETIC_FIELD);

			// New instance for event listener
			s2 = new mySensorEventListener(rotation);
			//pass mapview to mySensorEvent Listener
			s2.setMapView(mapview);
			s2.setNav(map);
			s2.setGraphListener(l1);
			
			sensorManager.registerListener(s2, accel,
					SensorManager.SENSOR_DELAY_FASTEST);
			sensorManager.registerListener(s2, accel2,
					SensorManager.SENSOR_DELAY_FASTEST);
			sensorManager.registerListener(s2, magnet,
					SensorManager.SENSOR_DELAY_FASTEST);
			// For graph
			SensorEventListener g1 = new mySensorEventListener(accelGraph);
			sensorManager.registerListener(g1, accel,
					SensorManager.SENSOR_DELAY_FASTEST);
			//Set mapview
//			s2.setMapView(mapview);

			
			accelGraph.setVisibility(View.VISIBLE);
			
		
			
			lineLay.addView(mapview);
//			lineLay.addView(accelGraph);
			// lineLay.addView(accelerometer);
			lineLay.addView(rotation);

			
			
			// Clear button for max
			Button resetButton = new Button(rootView.getContext());
			lineLay.addView(resetButton);
			resetButton.setText("Reset Step");
			resetButton.setOnClickListener(new View.OnClickListener() {
				public void onClick(View A) {
					// Call method from mySensorEventListener to reset the max
					s2.resetMax();
					s2.resetSteps();
					s2.setNewOrigin();
				}
			});
			
			Button recalButton = new Button(rootView.getContext());
			lineLay.addView(recalButton);
			recalButton.setText("Reset Angle");
			recalButton.setOnClickListener(new View.OnClickListener() {
				public void onClick(View A) {
					s2.recalTheta();
				}
			});

			return rootView;
		}
	}

}
