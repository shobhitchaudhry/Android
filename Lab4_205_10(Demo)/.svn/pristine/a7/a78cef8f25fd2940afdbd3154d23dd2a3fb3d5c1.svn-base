package ca.uwaterloo.lab4_205_10;

import java.util.ArrayList;
import java.util.List;

import mapper.InterceptPoint;
import mapper.MapView;
import mapper.NavigationalMap;
import mapper.PositionListener;
import android.content.Context;
import android.graphics.PointF;
import android.widget.Toast;
class LinePlotter implements PositionListener {
	
//private static MapView mapview;
//private static PointF start;
//private static PointF dest;
	float mapHeight=19;
	PointF start= new PointF();
	PointF end= new PointF();
	PointF pathPts= new PointF();
	NavigationalMap navMap=new NavigationalMap();
	private List<PointF> instaPath = new ArrayList<PointF>();
	private List<PointF> mainPath = new ArrayList<PointF>();
	List<InterceptPoint> intersections = new ArrayList<InterceptPoint>();
	List<InterceptPoint> mapHeightPt = new ArrayList<InterceptPoint>();

//Constructor 
public LinePlotter(NavigationalMap in)
{
	navMap = in;
}
//	public static void PlotLine(){
//
//		List<PointF> userPath = new ArrayList<PointF>();
//		userPath.add(start);
//		userPath.add(dest);
//		mapview.setUserPath(userPath);
//		//TODO algorithm to compute path without walking into wall
//	}

	@Override
	public void originChanged(MapView source, PointF loc) {
		start = loc;

		//Reset the user location at the current location
//		start = MainActivity.mapview.getOriginPoint();
//
//		MainActivity.mapview.setUserPoint(start.x,start.y);
//		
//		//reset the position for the sensor to update correctly
		MainActivity.s2.resetMapXY(loc.x, loc.y);
//		PlotLine();

		source.setUserPoint(start);
		//toast(source, start.toString());
		if(end.length()!=0 && notCloseEnough(start, end, source))
		{
			generatePath(source, start, end);
		}
	}

	@Override
	public void destinationChanged(MapView source, PointF dest) {
//		start = MainActivity.mapview.getOriginPoint();
//		PlotLine();
//		
		
		end=dest;
		//toast(source, end.toString());
		if(start.length()!=0 && notCloseEnough(start, end, source))
		{
			generatePath(source,start, end);
		}
	}
	public void generatePath(MapView source, PointF start, PointF end)
	{
		intersections.addAll(navMap.calculateIntersections(start, end) );	
		if(intersections.size()==0)
		{
			instaPath.add(start);
			instaPath.add(end);
			source.setUserPath(instaPath);
			intersections.clear();	
			instaPath.clear();
		}
		else if(intersections.size()!=0){
				findLine(source, start, end);
		intersections.clear();
		}
	}
	
	public Boolean findLine(MapView source, PointF start, PointF end){
		
		for(float i=mapHeight; i>=2.2f; i=i-0.5f){
			for(float j=mapHeight; j>=2.2f; j=j-0.5f){
				PointF mainLine1= new PointF(start.x, i);
				PointF mainLine2=new PointF(end.x, j);
			
				if(navMap.calculateIntersections(mainLine1, mainLine2).size()==0){
						mainPath.add(start);
						mainPath.add(mainLine1);
						mainPath.add(mainLine2);
						mainPath.add(end);
					source.setUserPath(mainPath);
					mainPath.clear();
					mainLine1=null;
					mainLine2=null;
					return true;
				}
			}
		}
		return false;
	}
	
	public void toast(MapView source, String toToast){
		Context context = source.getContext();	
		CharSequence text = toToast;	
		int duration = Toast.LENGTH_SHORT;
		Toast toast = Toast.makeText(context, text, duration);
		toast.show();
	}
	
	public Boolean notCloseEnough(PointF one, PointF two, MapView source){
		float diffX=one.x-two.x;
		float diffY=one.y-two.y;
		float distance =(float) Math.pow((Math.pow(diffX, 2) + Math.pow(diffY, 2)), 0.5 ) ;
		if(distance<1 && start.length()!=0 && end.length()!=0){
			toast(source, "You have arrived at your destination !!!!!!!!");
			return false;
		}
		else {
			return true;
		}
	}
}
