---
layout: post
title: "简单使用ContentObserver观察数据库更新"
date: 2013-05-17 20:54
comments: true
categories: android
---

<!-- more -->

需要注意的是，使用了registerContentObserver后，一定要使用unregisterContentObserver，否则会多次注册引起多次onChange。

MainActivity.java
{% codeblock lang:java %}
public class MainActivity extends Activity {  
    private Button button;  
    private OnClickListener listener = new OnClickListener(){  
  
        @Override  
        public void onClick(View v) {  
            ContentResolver resolver = MainActivity.this.getContentResolver();  
            Uri uri = Uri.parse("content://com.example.test/person");  
            PersonObserver observer=new PersonObserver(new Handler());
            resolver.registerContentObserver(uri, true,observer);  
            ContentValues values = new ContentValues();  
            values.put("name", "zzz");  
            values.put("cid", 1);  
            resolver.insert(uri, values);   //向ContentProvider插入数据  
            resolver.unregisterContentObserver(observer);
        }  
    };  
    
    @Override  
    public void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
        button = (Button)this.findViewById(R.id.button1);  
        button.setOnClickListener(listener);  
    }  
    private class PersonObserver extends ContentObserver{//监听  
        public PersonObserver(Handler handler) {  
            super(handler);  
        }  
        //当ContentProvier数据发生改变，则触发该函数  
        @Override  
        public void onChange(boolean selfChange) {  
            super.onChange(selfChange);  
            Log.i("Test1", "数据改变");  
        }  
    }  
}
{% endcodeblock %}

PersonProvider.java
{% codeblock lang:java %}
public class PersonProvider extends ContentProvider {
	private ItemDatabaseHelper helper;
	private SQLiteDatabase db;
//	private UriMatcher matcher = new UriMatcher(UriMatcher.NO_MATCH);

	@Override
	public boolean onCreate() {
		helper = new ItemDatabaseHelper(this.getContext());
//		matcher.addURI("com.example.test", "person", 1);
		return true;
	}

	@Override
	public Cursor query(Uri uri, String[] projection, String selection,
			String[] selectionArgs, String sortOrder) {
		return null;
	}

	/*
	 * 如果操作集合，则必须以vnd.android.cursor.dir开头 如果操作非集合，则必须以vnd.android.cursor.item开头
	 */
	@Override
	public String getType(Uri uri) {
		return "";
	}

	@Override
	public Uri insert(Uri uri, ContentValues values) {
		db = helper.getWritableDatabase();
			long rowid = db.insert("person", null, values);
			this.getContext().getContentResolver().notifyChange(uri, null);// 如果改变数据，则通知所有人
			return ContentUris.withAppendedId(uri, rowid); // 返回插入的记录所代表的URI
	}

	@Override
	public int delete(Uri uri, String selection, String[] selectionArgs) {
		return 0;
	}

	@Override
	public int update(Uri uri, ContentValues values, String selection,
			String[] selectionArgs) {
		return 0;
	}

	private static class ItemDatabaseHelper extends SQLiteOpenHelper {

		public ItemDatabaseHelper(Context context) {
			super(context, "person", null, 1);
		}

		@Override
		public void onCreate(SQLiteDatabase database) {

			database.execSQL("create table if not exists person("
					+ " _id integer primary key autoincrement," + " name text,"
					+ "cid text" + ");");
		}

		@Override
		public void onUpgrade(SQLiteDatabase database, int oldVersion,
				int newVersion) {
			database.execSQL("drop table if exists items");
			onCreate(database);
		}
	}
}
{% endcodeblock %}
