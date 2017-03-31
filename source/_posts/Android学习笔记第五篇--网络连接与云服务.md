---
title: "Android网络连接与云服务"
date: 2017-03-27 16:57
author: 冰路梦
tag:
    - Android
categories:
    - Android
---
# Android学习笔记第五篇--网络连接与云服务

## 第一章、无线连接设备

​	除了能够在云端通讯，Android的无线API也允许在同一局域网内的设备通讯，**甚至没有连接网络，而是物理具体相近，也可以相互通讯。**Network Service Discovery 简称NSD可以允许应用相互通讯发现附近设备。

​	本节主要介绍Android应用发现与连接其他设备的API。主要介绍NSD的API和点对点无线(the Wi-Fi Peer-to-Peer)API。

### 1、使用网络服务发现(NSD)

添加NSD服务到App中，可以使用户辨识在局域网内支持app请求的设备。有助于更好的实现文件共享、联机游戏等服务需求。

- 注册NSD服务

  > **Note:**注册NSD服务为非必选项，若是不关注本地网络的广播，则可以不用注册。

  在局域网内注册自身服务首先要创建`NsdServiceInfo`对象。

  ```java
  public void registerService(int port){
    //创建并初始化NSD对象
    NsdServiceInfo serviceInfo = new NsdServiceInfo();
    //服务名称要保证唯一性
    serviceInfo.serServiceName("NsdChat");
    //指定协议和传输层，如指定打印服务"_ipp._tcp"
    serviceInfo.setServiceType("_http._tcp.");
    serviceInfo.setPort(port);
    .....
  }
  ```

  如上创建了一个NSD服务，并设置了名称、服务类型。其中服务类型制定的是应用使用的协议和传输层。语法是`_<protocol>._<transportlayer>`。

  > **Note:**互联网编号分配机构(International Assigned Numbers Authority)提供用于服务发现协议，如NSD和Bonjour等。

  服务端口号应避免硬代码，以便于可以动态更改端口号，并更新通知。

  ```java
  public void initializeServerSocket(){
    //初始化一个server socket，指定下面的端口
    mServiceSocket = new ServerSocket(0);
    //存储选择的端口号
    mLocalPort = mServerSocket.getLocalPort();
    ......
  }
  ```

  至此已经创建了`NsdServiceInfo`对象，接着要实现`RegistrationListener`接口，实现注册功能。

  ```java
  public void initializeRegistrationListener(){
    mRegistrationListener = new NsdManager.RegistrationListener(){
      @Override
      public void onServiceRegistered(NsdServiceInfo nsdServiceInfo){
        //需要更新已经保存的注册服务名称，因为它需要唯一性，若是命名冲突，Android会自动解决冲突，此处就需要更新获取。
        mServiceName = nsdServiceInfo.getServiceName();
      }
      @Override
      public void onRegistrationFailed(NsdServiceInfo serviceInfo,int errorCode){
       //注册失败时候，在此处可以记录日志 
      }
      @Override
      public void onServiceUnregistered(NsdServiceInfo arg0){
        //注销服务，只有通过NsdManager来注销才会调用这里。
      }
      
      @Override
      public void onUnregistrationFailed(NsdService serviceInfo,int errorCode){
       //注销失败，记录日志 
      }
    };
  }
  ```

  因为`registerService()`方法是异步的，在注册服务之后的操作，需要在`onServiceRegistered()`方法中进行。

  ```java
  public void registerService(int port){
    NsdServiceInfo serviceInfo = new NsdServiceInfo();
    serviceInfo.setServiceName("NsdChat");
    serviceInfo.setServiceType("_http._tcp.");
    serviceInfo.setPort(Port);
    
    mNsdManager = Context.getSystemService(Context.NSD_SERVICE);
    mNsdManager.registerService(serviceInfo,NsdManager.PROTOCOL_DNS_SD,mRegistrationListener);
  }
  ```

- 发现网络中的服务

  发现网络服务需要两步：

  - 注册网络监听器
  - 调用`discoverServices()`异步API

  1、创建`NsdManager.DiscoveryListener`接口的实现类。

  ```java
  public void initializeDiscoveryListener(){
    //实例化网络发现监听器
    mDiscoverListener = new NsdManager.DiscoveryListener(){
      //发现服务时候调用该方法
      @Override
      public void onDiscoveryStarted(String regType){
        Log.d(TAG,"Service discovery started");
      }
      @Override
          public void onServiceFound(NsdServiceInfo service) {
              // A service was found!  Do something with it.
              Log.d(TAG, "Service discovery success" + service);
              if (!service.getServiceType().equals(SERVICE_TYPE)) {
                  // Service type is the string containing the protocol and
                  // transport layer for this service.
                  Log.d(TAG, "Unknown Service Type: " + service.getServiceType());
              } else if (service.getServiceName().equals(mServiceName)) {
                  // The name of the service tells the user what they'd be
                  // connecting to. It could be "Bob's Chat App".
                  Log.d(TAG, "Same machine: " + mServiceName);
              } else if (service.getServiceName().contains("NsdChat")){
                  mNsdManager.resolveService(service, mResolveListener);
              }
          }

          @Override
          public void onServiceLost(NsdServiceInfo service) {
              // When the network service is no longer available.
              // Internal bookkeeping code goes here.
              Log.e(TAG, "service lost" + service);
          }

          @Override
          public void onDiscoveryStopped(String serviceType) {
              Log.i(TAG, "Discovery stopped: " + serviceType);
          }

          @Override
          public void onStartDiscoveryFailed(String serviceType, int errorCode) {
              Log.e(TAG, "Discovery failed: Error code:" + errorCode);
              mNsdManager.stopServiceDiscovery(this);
          }

          @Override
          public void onStopDiscoveryFailed(String serviceType, int errorCode) {
              Log.e(TAG, "Discovery failed: Error code:" + errorCode);
              mNsdManager.stopServiceDiscovery(this);
          }
      };
    }
  }
  ```

  `NSD API`通过使用该接口中的方法，可以对网络服务状态进行监控。设置好监听器后，调用`discoverService()`函数：

  ```java
  mNsdManager.discoveryService(SERVICE_TYPE,NsdManager.PROTOCOL_DNS_SD,mDiscoveryListener);
  ```

- 连接到网络上的服务

  发现网络上的可接入服务时，首先调用resolveService()方法，来确定服务连接信息。实现`NsdManage.ResolveListener`对象并将其传入`resolveService()`方法，并使用该对象获得`NsdSerServiceInfo`。

  ```java
  public void initializeResolveListener(){
    mResolveListener = new NsdManager.ResolveListener(){
      @Override
          public void onResolveFailed(NsdServiceInfo serviceInfo, int errorCode) {
              // Called when the resolve fails.  Use the error code to debug.
              Log.e(TAG, "Resolve failed" + errorCode);
          }

          @Override
          public void onServiceResolved(NsdServiceInfo serviceInfo) {
              Log.e(TAG, "Resolve Succeeded. " + serviceInfo);

              if (serviceInfo.getServiceName().equals(mServiceName)) {
                  Log.d(TAG, "Same IP.");
                  return;
              }
              mService = serviceInfo;
              int port = mService.getPort();
              InetAddress host = mService.getHost();
          }
    };
  }
  ```

  至此完成服务接入，即可实现本地与之通讯。

- 程序退出注销服务

  使用NSD服务是比较消耗资源的，而且重复链接会导致问题，所以需要在app生命周期内的合适阶段开启、关闭服务。

  ```java
  //Activity
  	@Override
      protected void onPause() {
          if (mNsdHelper != null) {
              mNsdHelper.tearDown();
          }
          super.onPause();
      }

      @Override
      protected void onResume() {
          super.onResume();
          if (mNsdHelper != null) {
              mNsdHelper.registerService(mConnection.getLocalPort());
              mNsdHelper.discoverServices();
          }
      }

      @Override
      protected void onDestroy() {
          mNsdHelper.tearDown();
          mConnection.tearDown();
          super.onDestroy();
      }

      // NsdHelper's tearDown method
          public void tearDown() {
          mNsdManager.unregisterService(mRegistrationListener);
          mNsdManager.stopServiceDiscovery(mDiscoveryListener);
      }
  ```

### 2、使用WiFi建立P2P连接

WiFi点对点(P2P)API允许应用程序在无需连接到网络和热点的情况下连接到附近的设备。相比于蓝牙技术，其具有加大的连接范围。

- 配置应用权限

  使用Wi-Fi P2P技术需要添加`CHANGE_WIFI_STATE`,`ACCESS_WIFI_STATE`以及`INTERNET`三种权限，因为虽然Wi-Fi P2P技术可以不用访问互联网，但是它使用的是`Java socket`的标准，所以需要`INTERNET`权限。

  ```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
            package="com.example.android.nsdchat"
            ...
  	<uses-permission
          android:required="true"
          android:name="android.permission.ACCESS_WIFI_STATE"/>
  	<uses-permission
          android:required="true"
          android:name="android.permission.CHANGE_WIFI_STATE"/>
      <uses-permission
          android:required="true"
          android:name="android.permission.INTERNET"/>
      ...
  ```

- 设置广播接收器和P2P管理器

  使用WiFi P2P时，需要侦听事件发生时的broadcast intent。需要`IntentFilter`

  - `WIFI_P2P_STATE_CHANGED_ACTION`指示Wi-Fi P2P是否开启
  - `WIFI_P2P_PEERS_CHANGED_ACTION`代表对等列表节点发生了变化。
  - `WIFI_P2P_CONNECTION_CHANGED_ACTION`表明Wi-Fi P2P连接状态发生了变化。
  - `WIFI_P2P_THIS_DEVICE_CHANGED_ACTION`指示设备详细配置发生了变化。

  ```java
  private final IntentFilter intentFilter = new IntentFilter();
  ...
  @Override
  public void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      setContentView(R.layout.main);

      //  Indicates a change in the Wi-Fi P2P status.
      intentFilter.addAction(WifiP2pManager.WIFI_P2P_STATE_CHANGED_ACTION);

      // Indicates a change in the list of available peers.
      intentFilter.addAction(WifiP2pManager.WIFI_P2P_PEERS_CHANGED_ACTION);

      // Indicates the state of Wi-Fi P2P connectivity has changed.
      intentFilter.addAction(WifiP2pManager.WIFI_P2P_CONNECTION_CHANGED_ACTION);

      // Indicates this device's details have changed.
      intentFilter.addAction(WifiP2pManager.WIFI_P2P_THIS_DEVICE_CHANGED_ACTION);

      ...
  }
  ```

  在`onCreate()`方法的最后，需要获得`WifiP2pManager`的实例，并调用他的`initailize()`方法，以获得`WifiP2pManager.Channel`对象.

  ```java
  Channel mChannel;
  @Override
  public void onCreate(Bundle savedInstanceState){
    ...
      mManager = (WifiP2pManager)getSystemService(Context.WIFI_P2P_SERVICE);
  	mChannel = mManager.initialize(this,getMainLooper(),null);
  }
  ```

  然后创建广播接收着，监听上述不同的P2P状态变化。

  ```java
  @Override
      public void onReceive(Context context, Intent intent) {
          String action = intent.getAction();
          if (WifiP2pManager.WIFI_P2P_STATE_CHANGED_ACTION.equals(action)) {
              // Determine if Wifi P2P mode is enabled or not, alert
              // the Activity.
              int state = intent.getIntExtra(WifiP2pManager.EXTRA_WIFI_STATE, -1);
              if (state == WifiP2pManager.WIFI_P2P_STATE_ENABLED) {
                  activity.setIsWifiP2pEnabled(true);
              } else {
                  activity.setIsWifiP2pEnabled(false);
              }
          } else if (WifiP2pManager.WIFI_P2P_PEERS_CHANGED_ACTION.equals(action)) {

              // The peer list has changed!  We should probably do something about
              // that.

          } else if (WifiP2pManager.WIFI_P2P_CONNECTION_CHANGED_ACTION.equals(action)) {

              // Connection state changed!  We should probably do something about
              // that.

          } else if (WifiP2pManager.WIFI_P2P_THIS_DEVICE_CHANGED_ACTION.equals(action)) {
              DeviceListFragment fragment = (DeviceListFragment) activity.getFragmentManager()
                      .findFragmentById(R.id.frag_list);
              fragment.updateThisDevice((WifiP2pDevice) intent.getParcelableExtra(
                      WifiP2pManager.EXTRA_WIFI_P2P_DEVICE));

          }
      }
  ```

  并在Activity启动时，注册广播，添加过滤器。Activity暂停或者关闭时候，注销广播。

  ```java
   //在Activity启动后注册广播
      @Override
      public void onResume() {
          super.onResume();
          receiver = new WiFiDirectBroadcastReceiver(mManager, mChannel, this);
          registerReceiver(receiver, intentFilter);
      }
  //Activity关闭前，注销广播。
      @Override
      public void onPause() {
          super.onPause();
          unregisterReceiver(receiver);
      }
  ```

- 初始化对等节点发现(Peer Discovery)

  调用`discoveryPeers()`开始搜寻附近设备，需要传入参数

  - 上面得到的`WifiP2pManager.Channel`对象。
  - 对`WifiP2pManager.ActionListener`接口的实现，确定发现成功与失败时候的事件处理。

  ```java
  mManager.discoverPeers(mChannel, new WifiP2pManager.ActionListener() {

          @Override
          public void onSuccess() {
              // Code for when the discovery initiation is successful goes here.
              // No services have actually been discovered yet, so this method
              // can often be left blank.  Code for peer discovery goes in the
              // onReceive method, detailed below.
          }

          @Override
          public void onFailure(int reasonCode) {
              // Code for when the discovery initiation fails goes here.
              // Alert the user that something went wrong.
          }
  });
  ```

  **注意：**如上仅仅完成了对匹配设备的发现扫描的初始化，`WifiP2pManager.ActionListener`中国年的方法会通知应用初始化是否正确等消息。

- 获取对等节点列表

  完成初始化后，扫描会得到匹配的附近设备列表信息。需要实现`WifiP2pManager.PeerListener`接口。

  ```java
  private List peers = new ArrayList();//匹配到的设备信息列表。
      ...

      private PeerListListener peerListListener = new PeerListListener() {
          @Override
          public void onPeersAvailable(WifiP2pDeviceList peerList) {

              // Out with the old, in with the new.
              peers.clear();
              peers.addAll(peerList.getDeviceList());

              // If an AdapterView is backed by this data, notify it
              // of the change.  For instance, if you have a ListView of available
              // peers, trigger an update.
              ((WiFiPeerListAdapter) getListAdapter()).notifyDataSetChanged();
              if (peers.size() == 0) {
                  Log.d(WiFiDirectActivity.TAG, "No devices found");
                  return;
              }
          }
      }
  ```

  如上获得的匹配列表，我们需要将它传递给广播接收者做进一步处理。

  ```java
  public void onReceive(Context context,Intent intent){
    ...
  	else if (WifiP2pManager.WIFI_P2P_PEERS_CHANGED_ACTION.equals(action)) {
          // Request available peers from the wifi p2p manager. This is an
          // asynchronous call and the calling activity is notified with a
          // callback on PeerListListener.onPeersAvailable()
          if (mManager != null) {
              mManager.requestPeers(mChannel, peerListListener);
          }
          Log.d(WiFiDirectActivity.TAG, "P2P peers changed");
      }...
  }
  ```

- 连接一个对等节点

  发现到附近可用设备，则可以进一步的连接它，需要创建一个新的WifiP2pConfig对象，并将连接信息从设备WifiP2pDevice拷贝到其中，调用connect()方法。

  ```java
      @Override
      public void connect() {
          // Picking the first device found on the network.
          WifiP2pDevice device = peers.get(0);

          WifiP2pConfig config = new WifiP2pConfig();
          config.deviceAddress = device.deviceAddress;
          config.wps.setup = WpsInfo.PBC;
  		//ActionListener仅实现通知初始化成功与否
          mManager.connect(mChannel, config, new ActionListener() {

              @Override
              public void onSuccess() {
                  // WiFiDirectBroadcastReceiver will notify us. Ignore for now.
              }

              @Override
              public void onFailure(int reason) {
                  Toast.makeText(WiFiDirectActivity.this, "Connect failed. Retry.",
                          Toast.LENGTH_SHORT).show();
              }
          });
      }
  ```

  使用`WifiP2pManager.ConnectionInfoListener`接口，`onConnectionInfoAvailable()`来确定连接状态。

  ```java
      @Override
      public void onConnectionInfoAvailable(final WifiP2pInfo info) {

          // InetAddress from WifiP2pInfo struct.
          InetAddress groupOwnerAddress = info.groupOwnerAddress.getHostAddress());

          // After the group negotiation, we can determine the group owner.
          if (info.groupFormed && info.isGroupOwner) {
              // Do whatever tasks are specific to the group owner.
              // One common case is creating a server thread and accepting
              // incoming connections.
          } else if (info.groupFormed) {
              // The other device acts as the client. In this case,
              // you'll want to create a client thread that connects to the group
              // owner.
          }
      }
  ```

  完善广播接收者的代码,监听到连接广播信号时候，请求连接。

  ```java
   ...
          } else if (WifiP2pManager.WIFI_P2P_CONNECTION_CHANGED_ACTION.equals(action)) {

              if (mManager == null) {
                  return;
              }

              NetworkInfo networkInfo = (NetworkInfo) intent
                      .getParcelableExtra(WifiP2pManager.EXTRA_NETWORK_INFO);

              if (networkInfo.isConnected()) {

                  // We are connected with the other device, request connection
                  // info to find group owner IP

                  mManager.requestConnectionInfo(mChannel, connectionListener);
              }
              ...
  ```

### 3、使用WiFi P2P服务

第一节讲述了`NSD`服务用于局域网之间的连接通讯，本节的WiFi P2P有点类似，但是并不相同。

- 配置Manifest

  需要网络权限以及wifi相关权限。如上节所讲的三个权限，配置在Android manifest清单文件中。

  ```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="com.example.android.nsdchat"
      ...

      <uses-permission
          android:required="true"
          android:name="android.permission.ACCESS_WIFI_STATE"/>
      <uses-permission
          android:required="true"
          android:name="android.permission.CHANGE_WIFI_STATE"/>
      <uses-permission
          android:required="true"
          android:name="android.permission.INTERNET"/>
      ...
  ```

- 添加本地服务

  需要在服务框架中注册该服务，才能对外提供。

  - 新建`WifiP2pServiceInfo`对象
  - 加入相应的服务详细信息
  - 调用`addLocalService()`来注册为本地服务。

  ```java
  private void startRegistration() {
          //  Create a string map containing information about your service.
          Map record = new HashMap();
          record.put("listenport", String.valueOf(SERVER_PORT));
          record.put("buddyname", "John Doe" + (int) (Math.random() * 1000));
          record.put("available", "visible");

          // Service information.  Pass it an instance name, service type
          // _protocol._transportlayer , and the map containing
          // information other devices will want once they connect to this one.
          WifiP2pDnsSdServiceInfo serviceInfo =
                  WifiP2pDnsSdServiceInfo.newInstance("_test", "_presence._tcp", record);

          // Add the local service, sending the service info, network channel,
          // and listener that will be used to indicate success or failure of
          // the request.
          mManager.addLocalService(channel, serviceInfo, new ActionListener() {
              @Override
              public void onSuccess() {
                  // Command successful! Code isn't necessarily needed here,
                  // Unless you want to update the UI or add logging statements.
              }

              @Override
              public void onFailure(int arg0) {
                  // Command failed.  Check for P2P_UNSUPPORTED, ERROR, or BUSY
              }
          });
  }
  ```

- 发现附近的服务

  新建一个`WifiP2pManager.DnsSdTxtRecordListener`实例来监听实时收到到记录。记录到的周边设备服务信息会拷贝到外部数据机构中，以供使用。

  ```java
  final HashMap<String, String> buddies = new HashMap<String, String>();
  ...
  private void discoverService() {
      DnsSdTxtRecordListener txtListener = new DnsSdTxtRecordListener() {
          @Override
          /* Callback includes:
           * fullDomain: full domain name: e.g "printer._ipp._tcp.local."
           * record: TXT record dta as a map of key/value pairs.
           * device: The device running the advertised service.
           */

          public void onDnsSdTxtRecordAvailable(
                  String fullDomain, Map record, WifiP2pDevice device) {
                  Log.d(TAG, "DnsSdTxtRecord available -" + record.toString());
                  buddies.put(device.deviceAddress, record.get("buddyname"));
              }
          };
      ...
  }
  ```

  然后创建`WifiP2pManager.DnsSdServiceResponseListener`对象，来响应服务请求。上述两个listener匹配构建后，调用`setDnsResponseListener()`将它们加入`WifiP2pManager`。

  ```java
  private void discoverService() {
  ...

      DnsSdServiceResponseListener servListener = new DnsSdServiceResponseListener() {
          @Override
          public void onDnsSdServiceAvailable(String instanceName, String registrationType,
                  WifiP2pDevice resourceType) {

                  // Update the device name with the human-friendly version from
                  // the DnsTxtRecord, assuming one arrived.
                  resourceType.deviceName = buddies
                          .containsKey(resourceType.deviceAddress) ? buddies
                          .get(resourceType.deviceAddress) : resourceType.deviceName;

                  // Add to the custom adapter defined specifically for showing
                  // wifi devices.
                  WiFiDirectServicesList fragment = (WiFiDirectServicesList) getFragmentManager()
                          .findFragmentById(R.id.frag_peerlist);
                  WiFiDevicesAdapter adapter = ((WiFiDevicesAdapter) fragment
                          .getListAdapter());

                  adapter.add(resourceType);
                  adapter.notifyDataSetChanged();
                  Log.d(TAG, "onBonjourServiceAvailable " + instanceName);
          }
      };

      mManager.setDnsSdResponseListeners(channel, servListener, txtListener);
      ...
  }
  ```

  调用`addServiceRequest()`创建服务请求,它需要一个listener来通知创建成功与否。

  ```java
   serviceRequest = WifiP2pDnsSdServiceRequest.newInstance();
          mManager.addServiceRequest(channel,
                  serviceRequest,
                  new ActionListener() {
                      @Override
                      public void onSuccess() {
                          // Success!
                      }

                      @Override
                      public void onFailure(int code) {
                          // Command failed.  Check for P2P_UNSUPPORTED, ERROR, or BUSY
                      }
                  });
  ```

  最后是调用`discoverService()`

  ```java
  mManager.discoverServices(channel, new ActionListener() {

              @Override
              public void onSuccess() {
                  // Success!
              }

              @Override
              public void onFailure(int code) {
                  // Command failed.  Check for P2P_UNSUPPORTED, ERROR, or BUSY
                  if (code == WifiP2pManager.P2P_UNSUPPORTED) {
                      Log.d(TAG, "P2P isn't supported on this device.");
                  else if(...)
                      ...
              }
          });
  ```

  顺利的话，可以实现匹配连接的效果，常见错误代码：

  - P2P_UNSUPPORTED 当前设备不支持
  - BUSY 系统繁忙
  - ERROR 内部错误