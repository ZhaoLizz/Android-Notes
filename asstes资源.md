# 使用assets资源

## 导入assets

- assest是精简的资源，它们也像资源那样打入apk包，但是不需要配置系统工具管理
- 用于想在代码中直接调用文件的情况
- 常用于需要大量加载图片和声音资源的情况

`右击app，new-Folder-Assets Floder-Finish创建资源目录，然后把声音文件等导入`

## 处理assets

- 创建一个**资源管理类**用于在应用中定位、管理记录以及播放**所有**asstes资源
- 使用`AssetManager`类访问assets，每个context只拥有一个AssetsManager，可以直接从contenxt获取
- `InputStream soundData = mAssets.open(assetPath)`可以通过打开asstes资源的完整路径名获取InputStream数据流

```java
//已经整合了完整的功能的BeatBox
public class BeatBox {
    private static final String TAG = "BeatBox";
    //sample_sound 是放置asstes资源的包目录
    private static final String SOUNDS_FOLDER = "sample_sounds";
    private static final int MAX_SOUND = 5;

    private AssetManager mAssets;
    private List<Sound> mSounds = new ArrayList<>();
    private SoundPool mSoundPool;

    public BeatBox(Context context) {
        mAssets = context.getAssets();
        //为了兼容API使用老的构造方法
        //参数：最多同时播放多少个音频、音频流类型、0
        mSoundPool = new SoundPool(MAX_SOUND, AudioManager.STREAM_MUSIC, 0);
        loadSounds();
    }

    public List<Sound> getSounds() {
        return mSounds;
    }

    //查询本地所有的assets 映射为Sound对象，存入List
    private void loadSounds() {
        String[] soundNames;
        try {
            //利用AssetManager访问assets文件
            //list方法能列出指定目录下的所有文件名
            soundNames = mAssets.list(SOUNDS_FOLDER);
            Log.i(TAG, "Found " + soundNames.length + " sounds");
        } catch (IOException ioe) {
            Log.e(TAG, "Cound not list assets", ioe);
            return;
        }

        //把所有Sound对象加入List
        //载入全部音频文件
        try {
            for (String filename : soundNames) {
                //加上包文件名以获取完整路径名
                String assetPath = SOUNDS_FOLDER + "/" + filename;
                Sound sound = new Sound(assetPath);
                load(sound);
                mSounds.add(sound);
            }
        } catch (IOException e) {
            Log.e(TAG, "Could not load sound ", e);
        }
    }


    //加载单个Sound进入SoundPool
    private void load(Sound sound) throws IOException {
        AssetFileDescriptor afd = mAssets.openFd(sound.getAssetPath());
        //把文件载入SoundPool待播,方法返回int型的id
        int soundId = mSoundPool.load(afd, 1);
        sound.setSoundId(soundId);
    }
}
```

## 使用assets

- 通过资源管理类获取到整个资源文件夹后，需要创建一个对象用来**管理单个资源**，包括资源文件名、用户应该看到的文件名以及其他一些相关信息

```java
public class Sound {
    private String mAssetPath;//完整路径名
    private String mName;     //文件名
    private Integer mSoundId;


    public Sound(String assetPath) {
        mAssetPath = assetPath;
        //分割单个资源的完整路径名得到一个String数组
        String[] components = assetPath.split("/");
        String filename = components[components.length - 1];
        //oldChar,newChar
        mName = filename.replace(".wav", "");
    }

    public String getAssetPath() {
        return mAssetPath;
    }

    public String getName() {
        return mName;
    }

    public Integer getSoundId() {
        return mSoundId;
    }

    public void setSoundId(Integer soundId) {
        mSoundId = soundId;
    }
}
```

# 音频播放

## 利用SoundPool类来管理音频播放

**创建SoundPool**

```java
public BeatBox(Context context) {
        mAssets = context.getAssets();

        //为了兼容API使用老的构造方法
        //参数：最多同时播放多少个音频、音频流类型、0
        mSoundPool = new SoundPool(MAX_SOUND, AudioManager.STREAM_MUSIC, 0);
        loadSounds();
    }
```

**使用SoundPool加载音频文件**

- 使用SoundPool播放前必须预先加载音频，音频文件(Sound对象)要有自己的Integer型id

```java
//BeatBox
//加载单个Sound进入SoundPool
    private void load(Sound sound ) throws IOException {
        AssetFileDescriptor afd = mAssets.openFd(sound.getAssetPath());
        //把文件载入SoundPool待播,方法返回int型的id
        int soundId = mSoundPool.load(afd, 1);
        sound.setSoundId(soundId);
    }
```

```java
//BeatBox的loadSounds方法
//把所有Sound对象加入List
        //载入全部音频文件
        try {
            for (String filename : soundNames) {
                //加上包文件名以获取完整路径名
                String assetPath = SOUNDS_FOLDER + "/" + filename;
                Sound sound = new Sound(assetPath);
                load(sound);
                mSounds.add(sound);
            }
        } catch (IOException e) {
            Log.e(TAG, "Could not load sound ", e);
        }
```

## 播放音频

```java
public void play(Sound sound) {
        Integer soundId = sound.getSoundId();
        if (soundId == null) {
            return;
        }
        //音频Id，左音量，右音量，优先级，是否循环(0否，-1是)，播放速率
        mSoundPool.play(soundId, 1.0f, 1.0f, 1, 0, 1.0f);
    }
```
