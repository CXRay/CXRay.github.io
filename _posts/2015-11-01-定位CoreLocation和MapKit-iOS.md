---
layout: post
date: 2015-11-01
title: 定位CoreLocation和MapKit
categories: iOS
---

使用CoreLocation需要在项目中先引入CoreLocation.framework，使用的类中代码引入CoreLocation

再info.plist表里面添加两个变量，NSLocationAlwaysUsageDescription和NSLocationWhenInUseUsageDescription，这两个ket的值将分别用于描述应用程序始终使用和使用期间使用定位的说明

通过定位服务管理类CLLocationManager来创建一个位置管理器，它能够给我们提供位置信息和高度信息，也可以监控设备进入或离开某个区域，还可以获得设备的运行方向

>初始化

    let locationManager = CLLocationManager()
    
CLLocationManager属性和方法

* location 位置
* desiredAccuracy  位置精度
* func startUpdatingLocation()  开启更新位置
* func stopUpdatingLocation()  停止更新位置
* func startUpdatingHeading()  开启更新方向
* func stopUpdatingLocation()  停止更新方向

>设置代理，添加CLLocationManagerDelegate,viewDidLoad中

    locationManager.delegate = self
    
>定位精度，viewDidLoad中

        locationManager.desiredAccuracy =         kCLLocationAccuracyBest
        
* kCLLocationAccuracyBestForNavigation    精度最高，一般用于导航
* kCLLocationAccuracyBest                 精确度最佳
* kCLLocationAccuracyNearestTenMeters     精确度10m以内
* kCLLocationAccuracyHundredMeters        精确度100m以内
* kCLLocationAccuracyKilometer            精确度1000m以内
* kCLLocationAccuracyThreeKilometers      精确度3000m以内

对于位置管理器启动更新后，更新将不断传递给位置管理器委托，直到停止更新，我们无法直接控制位置管理器更新的频率，但可使用位置管理器的distanceFilter属性进行间接控制。在启动更新前设置属性distanceFilter，它指定设备（水平或垂直）移动多少米后才将另一个更新发送给委托。定位要求的精度越高，属性distanceFilter的值越小，应用程序的耗电量就越大。它的单位是米，我们也可以直接通过整形数字来设置这个距离，viewDidLoad中

	locationManager.distanceFilter = 200
	
>创建两个按钮，一个用于发出授权申请，设备会弹出提示，请求用户允许使用定位服务，另一个用于停止刷新定位服务

	 @IBAction func askForLocation(sender: AnyObject) {
        locationManager.requestAlwaysAuthorization()
        
    }
    @IBAction func stopUpdateLocation(sender: AnyObject) {
        locationManager.stopUpdatingLocation()
    }
    
>通过didChangeAuthorizationStatus代理方法，可以获得设备是否运行使用定位服务

* NotDetermined：用户还没有被请求获取授权
* Restricted：用户在设置里关闭了位置服务
* Denied：用户收到获取授权的请求，但点击了NO，或者在设置里关闭了
* Authorized：用户收到获取授权的请求，点击了YES；（此状态在ios8废弃了，ios7以及以下可用）
* AuthorizedAlways = kCLAuthorizationStatusAuthorized用户授权app在任何时候获取位置信息
* AuthorizedWhenInUse：用户授权app在前台获取位置信息


		func locationManager(manager: CLLocationManager, didChangeAuthorizationStatus status: CLAuthorizationStatus) {
			 if status == CLAuthorizationStatus.AuthorizedAlways || status == CLAuthorizationStatus.AuthorizedWhenInUse
			 {
			     //允许使用定位
			     
			     //开始启动定位更新服务
			     locationManager.startUpdatingLocation()
			     print("定位开始")
			 }
			 else if status == CLAuthorizationStatus.Denied
			 {
			     print("请求被拒绝")
			 }
			 else if status == CLAuthorizationStatus.NotDetermined
			 {
			     print("用户还没有被请求获取授权")
			 }
			 else if status == CLAuthorizationStatus.Restricted
			 {
			     print("用户在设置里关闭了位置服务")
			 }
		 
		}
		
		
定位改变时，委托会执行以下方法，可以得到新位置，旧位置，locations数组里会有前后位置的经度纬度坐标值，可以定义一个CLLocation坐标对象，来接收坐标值

CLLocation对象中包涵定位点的相关位置数据，主要有经度纬度，海拔信息，可以通过属性和方法来获取

* coordinate   位置的经度和纬度
* altitude    位置的海拔
* horizontalAccuracy  位置的水平精度
* verticalAccuracy    位置的垂直精度
* course  位置的方向
* speed   位置的速度
* func distanceFromLocation(location: CLLocation) -> CLLocationDistance  两个位置之间的距离

		var currLocation :CLLocation!
		@IBOutlet weak var longitudeTxt: UILabel!
		@IBOutlet weak var latitudeTxt: UILabel!
		@IBOutlet weak var heightTxt: UILabel!
		
		func locationManager(manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
		    //获取最新坐标
		    currLocation = locations.last
		    //获取经度纬度海拔
		    longitudeTxt.text = "经度：\(currLocation.coordinate.longitude)"
		    latitudeTxt.text = "纬度：\(currLocation.coordinate.latitude)"
		    heightTxt.text = "高度：\(currLocation.altitude)"
		}

	    //两个坐标点的距离，这个方法只是为了说明func distanceFromLocation(location: CLLocation) -> CLLocationDistance的使用
	    func distanceTo()
	    {
	        var targetLocation = CLLocation(latitude: 53.203715, longitude: 50.160374)
	        var distance = currLocation.distanceFromLocation(targetLocation)
	        print(distance)
	    }
	    
使用以下方法可以获取设备移动的方向，参数newHeading是一个CLHeading对象，CLHeading通过一组属性来提供航向读数：magneticHeading和trueHeading。这些值的单位为度，类型为CLLocationDirection，即为双精度浮点数。trueHeading和magneticHeading分别表示真实航向和磁性航向，如果位置服务被关闭了，GPS和wifi就只能获取magneticHeading，只有打开位置服务，才能获取trueHeading（真实航向)如果航向为0.0，则前进方向为正北
航向为90.0，为东,航向为180.0，为南,航向为270.0，为西

CLHeading 类是显示位置方向的类，可以用于实现指南针功能，CLHeading常用的属性和方法如下

* magneticHeading  位置的磁极方向
* trueHeading   位置的真实方向
* headingAccuracy  方向的精度
* timestamp  获取Core Location确定位置时的时间戳
* description 获取方向数据

	    @IBOutlet weak var trueHeading: UITextField!
	    @IBOutlet weak var magneticHeading: UITextField!
	    func locationManager(manager: CLLocationManager, didUpdateHeading newHeading: CLHeading) {
	        locationManager.startUpdatingLocation()
	        trueHeading.text = "\(newHeading.trueHeading)"
	        magneticHeading.text = "\(newHeading.magneticHeading)"
	    }
	    
>当定位出现错误时就会调用的委托方法如下

	func locationManager(manager: CLLocationManager, didFailWithError error: NSError) {
	    print(error)
	    
	    if let clErr = CLError(rawValue: error.code)
	    {
	        switch clErr{
	        case .LocationUnknown:
	            print("位置不明")
	        case .Denied:
	            print("允许检索位置被拒绝")
	        case .Network:
	            print("用于检索位置的网络不可用")
	        default:
	            print("未知的位置错误")
	        }
	    }
	    else
	    {
	        print("其他错误")
	        let alert = UIAlertView(title: "提示信息", message: "定位失败", delegate: nil, cancelButtonTitle: "确定")
	        alert.show()
	    }
	}

public enum CLError : Int 

* case LocationUnknown // 位置目前未知。但CLLocation会继续努力获取
* case Denied // 获取位置功能被拒绝使用
* case Network // 一般情况下，网络相关错误
* case HeadingFailure // 获取的位置标题信息，不能确定
* case RegionMonitoringDenied // 位置区域已被用户监测
* case RegionMonitoringFailure // 监测器不能注册
* case RegionMonitoringSetupDelayed // 不能初始化检测器CL
* case RegionMonitoringResponseDelayed // 区域监测响应延迟
* case GeocodeFoundNoResult // 地理编码无结果
* case GeocodeFoundPartialResult // 地理编码请求，得到部分结果
* case GeocodeCanceled // 地理编码请求被取消
* case DeferredFailed // 推迟模式失败
* case DeferredNotUpdatingLocation // 由于位置更新失败或暂停，推迟模式失败
* case DeferredAccuracyTooLow // 推迟模式不支持精度要求
* case DeferredDistanceFiltered // 推迟模式无法支持距离滤波器
* case DeferredCanceled // 推迟模式被取消
* case RangingUnavailable // 无法进行测距
* case RangingFailure // 通用测距故障

>地理信息反编码

通过CoreLocation类，得到的定位信息都是以经度和纬度表示的，一般都要把它反编码成一个地址，这就需要用到CLGeocoder类来实现地理信息反编码

    @IBAction func reverseGeocode(sender: AnyObject)
    {
        let geocoder = CLGeocoder()
        var p: CLPlacemark?
        geocoder.reverseGeocodeLocation(currLocation, completionHandler: { (placemarks, error) -> Void in
            //强制成中文
        let array = NSArray(object: "zh-hans")
        NSUserDefaults.standardUserDefaults().setObject(array, forKey: "AppleLanguages");
        
        //显示所有信息
        if error != nil
        {
            print("reverse geocode fail : \(error!.localizedDescription)")
            return
        }
        let pm = placemarks! as [CLPlacemark]
        if pm.count > 0
        {
            p = placemarks![0] as CLPlacemark
            print(p)//输出反编码信息
        }
        else
        {
            print("No Placemarks")
        }
        })
    }
    
>地理信息编码,同样可以根据一个地址来编码获取经度纬度

    @IBAction func locationBianMa(sender: AnyObject) {
        //使用Google服务进行地理编码
        var geocoder = CLGeocoder()
        var p: CLPlacemark?
        geocoder.geocodeAddressString("北京海淀区北三环西路 39 号", completionHandler: { (placemarks,error) -> Void in
            if error != nil{
                print("reverse geocode fail : \(error?.localizedDescription)")
                return
            }
            let pm = placemarks! as [CLPlacemark]
            if pm.count > 0
            {
                p = placemarks![0] as CLPlacemark
                print("Longitude = \(p?.location?.coordinate.longitude)")
                print("Latitude = \(p?.location?.coordinate.latitude)")
                print(p)
            }
            else
            {
                print("No Placemarks")
            }
        })
    }
    
#MapKit

MapKit框架主要提供了四个功能，显示地图，CLLocation和地址之间的转换，支持在地图上做标记 ，把一个位置解析成地址，需要导入MapKit.framework和import MapKit

>可以代码创建,这里用storyboard创建

    //var mapView = MKMapView(frame: CGRectMake(0,0,320,200))
    @IBOutlet weak var mainMapView: MKMapView!
    
地图显示的区域由region属性定义，如下
func setRegion(region: MKCoordinateRegion,animated: Bool)

MKCoordinateRegion类型结构如下

	struct MKCoordinateRegion {
	        center :CLLocationCoordinate2D
	        span :MKCoordinateSpan
	    } 
	    
MKCoordinateSpan结构也包含两个成员

	struct MKCoordinateSpan {
        var latitudeDelta: CLLocationDegrees   这个类型实际上也就是double类型
        var longitudeDelta: CLLocationDegrees
    }
    
>创建一个获取位置的button

    @IBAction func mapLocation(sender: AnyObject) {
        //MapKit
        //创建一个MKCoordinateSpan对象，设置地图的范围，越小越精确
        let latDelta = 0.05
        let longDelta = 0.05
        let currentLocationSpan = MKCoordinateSpanMake(latDelta, longDelta)
        
        //定义一个区域，坐标使用定位获取的当前坐标
        let currentRegion = MKCoordinateRegionMake((locationManager.location?.coordinate)!, currentLocationSpan)
        self.mainMapView.setRegion(currentRegion, animated: true)
        
        //通过mapType属性，可以设置地图的显示类型，比如标准地图模式，卫星模式，混合模式，具体代码如下
        //设置地图显示类型
        self.mainMapView.mapType = MKMapType.Satellite
        /*Standard标准地图，Satellite卫星，Hybrid混合地图，，SatelliteFlyover，HybridFlyover,Flyover城市观光的意思*/
        
        //可以在任何坐标位置添加大头针
        let objectAnnotation = MKPointAnnotation()
        //设置大头针显示位置，我们使用定位到的坐标
        objectAnnotation.coordinate = (locationManager.location?.coordinate)!
        //设置大头针之后显示的标题
        objectAnnotation.title = "香港"
        //设置点击大头针之后的显示的描述
        objectAnnotation.subtitle = "一个好地方"
        //添加大头针
        self.mainMapView.addAnnotation(objectAnnotation)
        
        //添加代理
        mainMapView.delegate = self
    }
    
>代理的介绍

    func mapView(mapView: MKMapView, regionWillChangeAnimated animated: Bool) {
        print("地图的缩放级别发生改变时")
    }
    
    func mapView(mapView: MKMapView, regionDidChangeAnimated animated: Bool) {
        print("地图的缩放完毕触发")
    }
    
    func mapViewWillStartLoadingMap(mapView: MKMapView) {
        print("开始加载地图")
    }
    
    func mapViewDidFinishLoadingMap(mapView: MKMapView) {
        print("结束加载地图")
    }
    
    func mapViewDidFailLoadingMap(mapView: MKMapView, withError error: NSError) {
        print("加载失败")
    }
    
    func mapViewWillStartRenderingMap(mapView: MKMapView) {
        print("开始渲染下载的地图块时调用")
    }
    
    func mapViewDidFinishRenderingMap(mapView: MKMapView, fullyRendered: Bool) {
        print("渲染下载的地图结束时调用")
    }
    
    //自定义大头针样式
    func mapView(mapView: MKMapView, viewForAnnotation annotation: MKAnnotation) -> MKAnnotationView? {
        if annotation is MKUserLocation
        {
            //return nil so map view draws "blue dot" for standard user location
            return nil
        }
        let reuseId = "pin"
        
        var pinView = mapView.dequeueReusableAnnotationViewWithIdentifier(reuseId) as? MKPinAnnotationView
        
        if pinView == nil
        {
            //创建一个大头针视图
            pinView = MKPinAnnotationView(annotation: annotation, reuseIdentifier: reuseId)
            pinView?.canShowCallout = true
            pinView?.animatesDrop = true
            
            //设置大头针颜色,这个以前只能用pinColor，现在可以使用其他颜色了
            pinView?.pinTintColor = UIColor.blackColor()
            
            //设置大头针点注释视图的右侧按钮样式
            pinView?.rightCalloutAccessoryView = UIButton(type: .DetailDisclosure)
            
        }
        else
        {
            pinView?.annotation = annotation
        }
        return pinView
    }
    
    func mapView(mapView: MKMapView, didAddAnnotationViews views: [MKAnnotationView]) {
        print("添加注释视图")
    }
    
    func mapView(mapView: MKMapView, annotationView view: MKAnnotationView, calloutAccessoryControlTapped control: UIControl) {
        print("点击注释视图按钮")
    }
    
    func mapView(mapView: MKMapView, didSelectAnnotationView view: MKAnnotationView) {
        print("点击大头针注释视图")
    }
    
    func mapView(mapView: MKMapView, didDeselectAnnotationView view: MKAnnotationView) {
        print("取消点击大头针注释视图")
    }
    
    func mapViewWillStartLocatingUser(mapView: MKMapView) {
        print("正在跟踪用户的位置")
    }
    
    func mapViewDidStopLocatingUser(mapView: MKMapView) {
        print("停止跟踪用户的位置")
    }
    
    func mapView(mapView: MKMapView, didUpdateUserLocation userLocation: MKUserLocation) {
        print("更新用户的位置")
    }
    
    func mapView(mapView: MKMapView, didFailToLocateUserWithError error: NSError) {
        print("跟踪用户的位置失败")
    }
    
    func mapView(mapView: MKMapView, annotationView view: MKAnnotationView, didChangeDragState newState: MKAnnotationViewDragState, fromOldState oldState: MKAnnotationViewDragState) {
        //移动annotation位置时调用，newState为宏，表示几个状态，是否能移动位置在annotation中设置
        print("移动annotation位置时调用")
    }
    
    func mapView(mapView: MKMapView, didChangeUserTrackingMode mode: MKUserTrackingMode, animated: Bool) {
        print("改变UserTrackingMode")
        
        /*userTrackingMode：跟踪类型，是一个枚举：
        MKUserTrackingMode.None :不进行用户位置跟踪；
        MKUserTrackingMode.Follow :跟踪用户位置；
        MKUserTrackingMode.FollowWithHeading :跟踪用户位置并且跟踪用户前进方向；*/
    }

    func mapView(mapView: MKMapView, rendererForOverlay overlay: MKOverlay) -> MKOverlayRenderer {
        //设置overlay的渲染
        let a = MKOverlayRenderer(overlay: overlay)
        return a
    }
    
    func mapView(mapView: MKMapView, didAddOverlayRenderers renderers: [MKOverlayRenderer]) {
        print("地图上加上了OverlayRenderers调用")
    }
