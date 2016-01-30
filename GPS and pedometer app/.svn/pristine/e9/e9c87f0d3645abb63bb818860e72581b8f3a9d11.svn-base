package ca.uwaterloo.lab4_205_10;

import java.util.ArrayList;
import java.util.List;

import mapper.InterceptPoint;
import mapper.MapView;
import mapper.NavigationalMap;
import android.graphics.PointF;
import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
import android.widget.TextView;

class mySensorEventListener implements SensorEventListener {
	TextView output;
	LineGraphView graph;
	MapView mapview;
	LinePlotter L;
	// variable for state machine
	private float x, y, z;
	private float mx = 0;
	private float my = 0;
	private float mz = 0;
	
	private float oldPt = 0;
	private int state = 0;
	private int steps = 0;
	private float distance = 0;
	// variable for rotation
	private float oldX = 0;
	private float oldY = 0;
	private float filtratedRad;
	public float zeroOut=0;
	public boolean recalCheck=false;
	private float recalTheta;
	private float xStep = 0;
	private float yStep = 0;
	private float netDisp=0 ;
	private float azimut,rotationDegree;
	private float[] accelMatrix = new float[3];
	private float[] magnetMatrix = new float[3];
	private float[] rotationMatrix = new float[9];
	private float[] orientationMatrix = new float[3];
	
	
	//Variable for map
	private float mapx = MainActivity.mapview.getOriginPoint().x;;
	private float mapy = MainActivity.mapview.getOriginPoint().y;
	//Is a step made?
	private boolean madeStep = false;
	//In front of a wall?
	private boolean wall = false;
	private List<InterceptPoint> intersectPt = new ArrayList<InterceptPoint>();
	private NavigationalMap map;
	
	//Reset mapx mapy to the origin
	public void setMapView(MapView mapview){
		this.mapview = mapview;
	}
	public void setNav(NavigationalMap map){
		this.map = map;
		
	}
	public void setGraphListener(LinePlotter L){
		this.L = L;
	}
	public void resetMapXY(float x, float y){
		this.mapx = x;
		this.mapy = y;
		mapview.setUserPoint(x, y);
		
	}
	public void setNewOrigin(){
		PointF updateOrigin = new PointF(this.mapx,this.mapy);
		mapview.setOriginPoint(updateOrigin);
		List<PointF> userPath = new ArrayList<PointF>();
		userPath.add(updateOrigin);
		userPath.add(mapview.getDestinationPoint());
		mapview.setUserPath(userPath);
	}
	// Constructor for Textview
	public mySensorEventListener(TextView outputView) {
		output = outputView;
	}

	// Constructor for Graph
	public mySensorEventListener(LineGraphView graph) {
		this.graph = graph;
	}
	
	public int getSteps() {
		return steps;
	}

	public float getRotation() {
		return rotationDegree;
	}

	public float normXYZ(float[] xyz) {

		double x = (double) xyz[0];
		double y = (double) xyz[1];
		double z = (double) xyz[2];
		double norm = Math.sqrt(Math.pow(x, 2) + Math.pow(y, 2)
				+ Math.pow(z, 2));

		float finalVal = (float) norm;
		return finalVal;
	}

	// Reset the max value
	public void resetMax() {
		this.mx = 0;
		this.my = 0;
		this.mz = 0;
	}

	public void resetSteps() {
		this.steps = 0;
		this.distance = 0;
		this.xStep = 0;
		this.yStep = 0;
		this.netDisp = 0;
	}

	public float lowPassFilter(float ptFiltrated, float ptNew, float alpha) {
		float filtrate = alpha * ptFiltrated + (1 - alpha) * ptNew;
		return filtrate;
	}

	public void onAccuracyChanged(Sensor s, int i) {
	}

	@Override
	public void onSensorChanged(SensorEvent se) {
		// Filter the raw data on sensor event update

		// Execute this block if textview or mapview constructor was used
		if (output != null) {
			
			if (se.sensor.getType() == Sensor.TYPE_LINEAR_ACCELERATION) {
				float filtrated = lowPassFilter(oldPt, se.values[1], 0.65f);
				oldPt = filtrated;

				x = se.values[0];
				y = se.values[1];
				z = se.values[2];

				// Find and update max of x y z as time passes
				mx = (Math.abs(x) > mx) ? Math.abs(x) : mx;
				my = (Math.abs(y) > my) ? Math.abs(y) : my;
				mz = (Math.abs(z) > mz) ? Math.abs(z) : mz;

				// return state to 0 if exceeding the max value or it is at
				// 0
				if (Math.abs(filtrated) > 10) {
					state = 0;
				} else if (Math.abs(filtrated) < 0.15) {
					// only count step if the whole cycle has been completed
					if (state == 4) {
						steps++;
						state = 0;
						distance = distance + 0.62f;
						yStep = yStep + (float) (0.62*Math.cos(recalTheta));
						xStep = xStep + (float) (0.62*Math.sin(recalTheta));
						netDisp = (float) Math.sqrt(Math.pow(yStep, 2)+Math.pow(xStep,2)); 
						
						//FIX
						madeStep = true;
						
					}
					// if the acceleration goes up by a bit and fall back
					// return to state 0
				} else if ((filtrated >= 0.15) && (filtrated < 0.45)) {
					if (state == 0) {
						state = 1;
					} else if (state == 2) {
						state = 3;
					}
				} else if ((filtrated >= 0.45) && (filtrated <= 10)) {
					if (state == 1) {
						state = 2;
					}
				} else if ((filtrated <= -0.15) && (filtrated > -10)) {
					if (state == 3) {
						state = 4;
					}
				}

			}

			if (se.sensor.getType() == Sensor.TYPE_ACCELEROMETER)
				accelMatrix = se.values;
			
			if (se.sensor.getType() == Sensor.TYPE_MAGNETIC_FIELD)
				magnetMatrix = se.values;

			if (magnetMatrix != null && accelMatrix != null) {
				boolean didItWork = SensorManager.getRotationMatrix(
						rotationMatrix, null, accelMatrix, magnetMatrix);
				//If every sensor works then proceed to calculation
				if (didItWork) {
					SensorManager.getOrientation(rotationMatrix,
							orientationMatrix);
					azimut = orientationMatrix[0] ;
					
					float currentX = (float) (100 * Math.cos(azimut));
					float currentY = (float)(100 * Math.sin(azimut));
					float filtY = lowPassFilter(oldY, currentY, 0.8f);
					float filtX = lowPassFilter(oldX, currentX, 0.8f);
					oldX = filtX;
					oldY = filtY;
					
					filtratedRad = (float)(Math.atan2(filtY, filtX));
					recalTheta=filtratedRad+zeroOut;
					rotationDegree = (float) (Math.toDegrees(recalTheta) + 360) % 360;
					
					output.setText(String.format("State: %d \n"
							+ "Total steps :%d \n"
							+ "Total distance traveled : %.2f m \n"
							+"Rotation Degree : %.0f \n"
							+ "North-East vector: [ %.2f , %.2f ] \n"
							+ "Net displacement: %.2f \n"
							+ "Map X Y = [ %.2f, %.2f ]",
							
							state, steps, distance,
							rotationDegree, yStep, xStep, netDisp,mapx, mapy));
				}
			}

			//FIX
			if(madeStep ==true){
				
				PointF current = new PointF(mapx, mapy);
				mapy = mapy - (float) (0.65f*Math.cos(recalTheta));
				mapx = mapx + (float) (0.65f*Math.sin(recalTheta));
				PointF next = new PointF(mapx , mapy);
				
				 
				intersectPt = map.calculateIntersections(current, next);
				
				if(intersectPt.size() != 0){
					next = current;
					wall = true;
				}
				
				if(wall){
					mapx = next.x;
					mapy = next.y;
				}
				
				mapview.setUserPoint(next);
				List<PointF> userPath = new ArrayList<PointF>();
				userPath.add(next);
				userPath.add(mapview.getDestinationPoint());
				
				if(next.length()!=0 && 
					L.notCloseEnough(next, mapview.getDestinationPoint(),
						mapview))
				{
					L.generatePath(mapview,next, mapview.getDestinationPoint());
				}
				else{
//				mapview.setUserPath(userPath);
				}
				madeStep = false;
			}
			

			// --------------------------------------------------------------------------------

		} else if (graph != null) {
			float filtrated = lowPassFilter(oldPt, se.values[1], 0.9f);
			oldPt = filtrated;
			// add the filtrate
			float[] addNorm = { 0, 0, filtrated };
			graph.addPoint(addNorm);

		}

	}
	public void recalTheta(){
		zeroOut=-(filtratedRad);
		recalCheck=true;
	}
}
