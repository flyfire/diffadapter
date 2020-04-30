+ [如何优雅的实现一个高效、高性能、异步数据实时刷新的列表](https://silencedut.github.io/2019/01/24/如何优雅的实现一个高效、高性能、异步数据实时刷新的列表/)
+ [一种基于接口、简洁易用的通信框架——Hub](https://silencedut.github.io/2018/08/12/一种基于接口化、清晰易读的路由框架——Hub/)
+ [Router—一个高效，使用方便，基于动态代理实现的Android事件总线库](https://silencedut.github.io/2016/09/04/Router—一个高效，使用方便，基于动态代理实现的Android事件总线库/)

``Transfer.getImpl(ILegendDateProvider::class.java)``可以得到``ILegendDateProvider``的具体实现类``LegendDataProviderImpl``。

``Transfer.getImpl(ILegendDateProvider::class.java).fetchLegends()``会调用``LegendDataProviderImpl.fetchLegends()``，在此方法中，生成随机结果后，调用``Transfer.getSubscriber(LegendNotification.LegendsList::class.java).onLegendsFetched(result)``，其中，``Transfer.getSubscriber(LegendNotification.LegendsList::class.java)``会获取到``LegendViewModel``，在``LegendViewModel``的构造方法中调用了``Transfer.subscribe(this)``进行注册。这样就将结果传递给了``LegendViewModel``。``LegendViewModel``的``onLegendsFetched``方法中会进行``LiveData.setValue``。进而引发``DiffAdapter.setData``，触发``AsyncListUpdateDiffer.submitList``

局部更新一个item如何实现？

``LegendDataProviderImpl.updateLegendNameAndPrice``会触发``Transfer.getSubscriber(LegendNotification.LegendInfo::class.java).onLegendPriceFetched(legendPrice)``也即会调用``LegendViewModel.onLegendPriceFetched``，会触发``legendPriceData``这个``LiveData.setValue``，进而引发``DiffAdapter``中``mUpdateMediatorLiveData``这个``MediatorLiveData``中``onChanged``方法调用，在``onChanged``方法中进行了``updateData``操作，``updateData``中进行了``mDatas.set(foundIndex, newData);``同时进行了``notifyItemChanged``。