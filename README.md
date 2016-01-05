# LoopViewPager
#		无限循环的viewpager
![](https://github.com/Clance/LoopViewPager/raw/master/screenshots/123.gif)  
##		[原地址在安卓巴士，现在正式迁移过来](http://www.apkbus.com/forum.php?mod=viewthread&tid=240971&extra=)
####		真正的无限循环，使用简单
####		简单说下功能吧:无限轮播(`就算一张图片也会轮播，不会报错`)、手指滑动时暂停自动轮播、图片点击返回正确的position，切换流畅，效果好


#		使用方法：

####		1、xml  
		<com.clj.loopviewpager.LoopViewPager
        android:id="@+id/loopviewpager"
        android:layout_width="match_parent"
        android:layout_height="100dp">
    	</com.clj.loopviewpager.LoopViewPager>
    	
####		2、activity 
		只需要像viewpager一样设置adapter就行了
```		
public class LoopViewPagerActivity extends AppCompatActivity {
    private LoopViewPager mLoopViewPager;
    private ArrayList<String> mUris;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_loopviewpager);
        initData();
    }

    private void initData() {
        mUris = new ArrayList<>();
        mUris
                .add("http://img3.imgtn.bdimg.com/it/u=214865969,2582156998&fm=11&gp=0.jpg");
        mUris
                .add("http://wenwen.soso.com/p/20131209/20131209133813-334451836.jpg");
        mUris
                .add("http://picview01.baomihua.com/photos/20120626/m_14_634763259911718750_11073026.jpg");
        mUris
                .add("http://tupian.qqjay.com/u/2012/0202/7e1354c474562247f0bda889bc2af2b4.jpg");
        mLoopViewPager = (LoopViewPager) findViewById(R.id.loopviewpager);
        mLoopViewPager.setAdapter(new MyAdapter(this, mUris));
        //如果图片在最后一张跳转时有闪烁，可以设置为TURE
        mLoopViewPager.setBoundaryCaching(true);


        ImageLoaderConfiguration.Builder config = new ImageLoaderConfiguration.Builder(
                this);
        config.threadPriority(Thread.MIN_PRIORITY + 2);
        config.denyCacheImageMultipleSizesInMemory();
        config.discCacheFileNameGenerator(new Md5FileNameGenerator());
        config.discCacheSize(50 * 1024 * 1024); // 50 MiB
        config.tasksProcessingOrder(QueueProcessingType.LIFO);
        config.threadPoolSize(3);
        config.writeDebugLogs();
        config.memoryCache(new UsingFreqLimitedMemoryCache(2 * 1024 * 1024))
                .memoryCacheSize(2 * 1024 * 1024);
        // Initialize ImageLoader with configuration.
        ImageLoader.getInstance().init(config.build());
    }


    private class MyAdapter extends PagerAdapter {

        /**
         * 图片资源列表
         */
        private ArrayList<String> mAdList = new ArrayList<String>();
        private ArrayList<View> mAdView = new ArrayList<View>();
        private Context mContext;
        private DisplayImageOptions mOptions;

        public MyAdapter(Context context, ArrayList<String> adList) {
            this.mContext = context;
            this.mAdList = adList;
            mOptions = new DisplayImageOptions.Builder()
                    .showImageOnFail(R.drawable.ic_launcher)
                    .imageScaleType(ImageScaleType.EXACTLY)
                    .resetViewBeforeLoading(true).cacheOnDisc(true)
                    .bitmapConfig(Bitmap.Config.RGB_565)
                    .considerExifParams(true).build();
        }

        @Override
        public int getCount() {
            return mAdList.size();
        }

        @Override
        public boolean isViewFromObject(View view, Object obj) {
            return view == obj;
        }

        @Override
        public Object instantiateItem(ViewGroup container, final int position) {
            final String imageUrl = mAdList.get(position);
            View view = LayoutInflater.from(mContext).inflate(
                    R.layout.header_viewpager, null);
            ImageView imageView = (ImageView) view
                    .findViewById(R.id.header_imageview);
            // 设置图片点击监听
            imageView.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Uri uri = Uri.parse(imageUrl);
                    Intent it = new Intent(Intent.ACTION_VIEW, uri);
                    startActivity(it);
                }
            });
            ImageLoader.getInstance().displayImage(imageUrl, imageView,
                    mOptions);
            mAdView.add(view);
            container.addView(view);
            return view;
        }

        @Override
        public void destroyItem(ViewGroup container, int position, Object object) {
            // 这里不需要做任何事情
        }

    }
}
```
