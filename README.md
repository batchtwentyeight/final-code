#admin_add.java

package example.com.helpdesk;

import android.app.ListActivity;
import android.app.ProgressDialog;
import android.content.Context;
import android.content.Intent;
import android.content.SharedPreferences;
import android.net.Uri;
import android.os.AsyncTask;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;
import android.view.View;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ImageView;
import android.widget.ListAdapter;
import android.widget.ListView;
import android.widget.ProgressBar;
import android.widget.SimpleAdapter;
import android.widget.Spinner;
import android.widget.Toast;

import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import android.app.AlertDialog;
import android.content.Context;
import android.content.DialogInterface;
import android.content.SharedPreferences;
import android.net.Uri;
import android.provider.MediaStore;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.text.Layout;
import android.text.TextUtils;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.EditText;
import android.widget.ImageView;
import android.widget.Spinner;

import java.util.ArrayList;
import java.util.List;

import android.app.ListActivity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.AsyncTask;
import android.os.Bundle;
import android.support.design.widget.FloatingActionButton;
import android.support.design.widget.NavigationView;
import android.support.design.widget.Snackbar;
import android.support.v4.view.GravityCompat;
import android.support.v4.widget.DrawerLayout;
import android.support.v7.app.ActionBarDrawerToggle;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.Toolbar;
import android.util.Log;
import android.view.MenuItem;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ListAdapter;
import android.widget.ListView;
import android.widget.SimpleAdapter;
import android.widget.Spinner;
import android.widget.TableLayout;
import android.widget.TableRow;
import android.widget.TextView;
import android.widget.Toast;

import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

import android.app.AlertDialog;
import android.content.DialogInterface;
import android.content.Intent;
import android.content.pm.PackageManager;
import android.database.Cursor;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.media.MediaPlayer;
import android.net.Uri;
import android.os.AsyncTask;
import android.os.Environment;
import android.provider.MediaStore;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ImageView;
import android.widget.ProgressBar;
import android.widget.RadioButton;
import android.widget.RadioGroup;
import android.widget.TextView;
import android.widget.Toast;
import android.widget.VideoView;

import com.google.android.gms.common.ConnectionResult;
import com.google.android.gms.common.GooglePlayServicesUtil;
import com.google.android.gms.gcm.GoogleCloudMessaging;
import com.loopj.android.http.AsyncHttpClient;
import com.loopj.android.http.AsyncHttpResponseHandler;
import com.loopj.android.http.RequestParams;

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.mime.content.FileBody;
import org.apache.http.entity.mime.content.StringBody;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;

import java.io.File;
import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Locale;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

import libmultispinner.MultiSelectionSpinner;

public class admin_add extends AppCompatActivity {

    EditText et_uid, et_lid, et_pwd, et_cno;
    Button add;
    String response,guid,glid,gpwd,gtype,gcno;
    long totalSize = 0;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_admin_add);
        et_uid           = (EditText) findViewById(R.id.input_uid);
        et_lid           = (EditText) findViewById(R.id.input_lid);
        et_pwd        = (EditText) findViewById(R.id.input_lpwd);
        et_cno        = (EditText) findViewById(R.id.input_cno);

        Intent iin= getIntent();
        Bundle b = iin.getExtras();

        if(b!=null)
        {
            gtype =(String) b.get("type");

        }

        add = (Button) findViewById(R.id.add);


        add.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {

                guid = et_uid.getText().toString();
                glid = et_lid.getText().toString();
                gpwd = et_pwd.getText().toString();
                gcno = et_cno.getText().toString();

                    if (guid != null && glid != null && gpwd != null && gcno!=null) {
                        if (isValid(gcno)) {
                            new UploadFileToServer().execute();
                        }
                        else {
                            Toast.makeText(getApplicationContext(), "Invalid Contact Number ", Toast.LENGTH_LONG).show();
                        }

                    } else {
                        Toast.makeText(getApplicationContext(), "Please Fill The Details Properly ", Toast.LENGTH_LONG).show();
                    }

            }
        });



    }

    public static boolean isValid(String s)
    {
        // The given argument to compile() method
        // is regular expression. With the help of
        // regular expression we can validate mobile
        // number.
        // 1) Begins with 0 or 91
        // 2) Then contains 7 or 8 or 9.
        // 3) Then contains 9 digits
        Pattern p = Pattern.compile("(0/91)?[7-9][0-9]{9}");

        // Pattern class contains matcher() method
        // to find matching between given number
        // and regular expression
        Matcher m = p.matcher(s);
        return (m.find() && m.group().equals(s));
    }

    @Override
    public void onBackPressed() {
        Intent intent = new Intent(admin_add.this, admin_Menu.class);
        startActivity(intent);
        finish();
    }

    private class UploadFileToServer extends AsyncTask<Void, Integer, String> {
        ProgressDialog pDialog;
        @Override
        protected void onPreExecute() {
            // setting progress bar to zero
            super.onPreExecute();
            pDialog = new ProgressDialog(admin_add.this);
            pDialog.setMessage("Please wait...");
            pDialog.setCancelable(false);
            pDialog.show();
        }

        @Override
        protected String doInBackground(Void... params) {
            return uploadFile();
        }

        @SuppressWarnings("deprecation")
        private String uploadFile() {
            String responseString = null;

            HttpClient httpclient = new DefaultHttpClient();
            HttpPost httppost = new HttpPost(Config.FILE_UPLOAD_URL1);

            try {
                AndroidMultiPartEntity entity = new AndroidMultiPartEntity(
                        new AndroidMultiPartEntity.ProgressListener() {

                            @Override
                            public void transferred(long num) {
                                publishProgress((int) ((num / (float) totalSize) * 100));
                            }
                        });

                entity.addPart("name",new StringBody(guid));
                entity.addPart("lid",new StringBody(glid));
                entity.addPart("pwd",new StringBody(gpwd));
                entity.addPart("type",new StringBody(gtype));
                entity.addPart("cno",new StringBody(gcno));

                totalSize = entity.getContentLength();
                httppost.setEntity(entity);

                // Making server call
                HttpResponse response = httpclient.execute(httppost);
                HttpEntity r_entity = response.getEntity();

                int statusCode = response.getStatusLine().getStatusCode();
                if (statusCode == 200) {
                    // Server response
                    responseString = EntityUtils.toString(r_entity);
                } else {
                    responseString = "Error occurred! Http Status Code: "+ statusCode;
                }

            } catch (ClientProtocolException e) {
                responseString = e.toString();
            } catch (IOException e) {
                responseString = e.toString();
            }

            return responseString;

        }

        @Override
        protected void onPostExecute(String result) {
            Log.d("PRINT", "Response from server: " + result);
            if (pDialog.isShowing())
                pDialog.dismiss();
            response = result;
            response = response.replaceAll("\\s+","");

                if (result.equals("inserted")) {
                    Toast.makeText(getApplicationContext(), "User Details Added Successfully", Toast.LENGTH_LONG).show();

                    Intent redirect = new Intent(admin_add.this,admin_add.class);
                    startActivity(redirect);
                    finish();

                }
                else if(result.equals("insertfailed")) {
                    Toast.makeText(getApplicationContext(), "Unable To Add User Details", Toast.LENGTH_LONG).show();
                }
                else if(result.equals("existed")) {
                    Toast.makeText(getApplicationContext(), "Given User ID Is Already In Use", Toast.LENGTH_LONG).show();
                }
                else if(result.equals("inputfailed")) {
                    Toast.makeText(getApplicationContext(), "Please Enter Proper Details ", Toast.LENGTH_LONG).show();
                }

            else {
                    Toast.makeText(getApplicationContext(), result, Toast.LENGTH_LONG).show();
                }

            super.onPostExecute(result);
        }

    }

}



# admin_Menu.java

package example.com.helpdesk;

import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Context;
import android.content.DialogInterface;
import android.content.Intent;
import android.content.SharedPreferences;
import android.os.AsyncTask;
import android.os.Bundle;
import android.support.design.widget.NavigationView;
import android.support.v4.view.GravityCompat;
import android.support.v4.widget.DrawerLayout;
import android.support.v7.app.ActionBarDrawerToggle;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.Toolbar;
import android.util.Log;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.AdapterView;
import android.widget.LinearLayout;
import android.widget.ListView;
import android.widget.SimpleAdapter;
import android.widget.TextView;
import android.widget.Toast;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;


public class admin_Menu extends AppCompatActivity
        implements NavigationView.OnNavigationItemSelectedListener {

    private static String iurl = Config.reset;
    private static String url = iurl;
String response = "";
    String[] service = new String[] {
            "Health Diagnosis",
            "Medicine Prescription",
            "Intimation For Tablet"
    };


    int[] flags = new int[]{
            R.drawable.dia1,
            R.drawable.dia2,
            R.drawable.dia3
    };

    String[] provider = new String[]{
            "A-Z Health Care",
            "Government Medical Association",
            "Government Medicine Corporation",
    };


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_admin_menu);
        Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);
        DrawerLayout drawer = (DrawerLayout) findViewById(R.id.drawer_layout);
        ActionBarDrawerToggle toggle = new ActionBarDrawerToggle(
                this, drawer, toolbar, R.string.navigation_drawer_open, R.string.navigation_drawer_close);
        drawer.setDrawerListener(toggle);
        toggle.syncState();
        NavigationView navigationView = (NavigationView) findViewById(R.id.nav_view);
        navigationView.setNavigationItemSelectedListener(this);


        SharedPreferences prefs = getSharedPreferences("UserDetails", Context.MODE_PRIVATE);
        Log.d("PRINT", "MA1 oncreate logged in: " + prefs.getString("login", "")+"    profile id: "+prefs.getString("profileid", ""));


        List<HashMap<String,String>> aList = new ArrayList<HashMap<String,String>>();

        for(int i=0;i<3;i++){
            HashMap<String, String> hm = new HashMap<String,String>();
            hm.put("txt", "Service Name : " + service[i]);
            hm.put("cur","Providers : " + provider[i]);
            hm.put("flag", Integer.toString(flags[i]) );
            aList.add(hm);
        }


        String[] from = { "flag","txt","cur" };
        int[] to = { R.id.flag,R.id.txt,R.id.cur};
        SimpleAdapter adapter = new SimpleAdapter(getBaseContext(), aList, R.layout.listview_layout, from, to);
        ListView listView = ( ListView ) findViewById(R.id.listview);
        listView.setAdapter(adapter);
        AdapterView.OnItemClickListener itemClickListener = new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View container, int position, long id) {
                LinearLayout linearLayoutParent = (LinearLayout) container;
                LinearLayout linearLayoutChild = (LinearLayout ) linearLayoutParent.getChildAt(1);
                TextView tvCountry = (TextView) linearLayoutChild.getChildAt(0);
                Toast.makeText(getBaseContext(), tvCountry.getText().toString(), Toast.LENGTH_SHORT).show();
            }
        };

        // Setting the item click listener for the listview
        listView.setOnItemClickListener(itemClickListener);

    }

    @Override
    public void onBackPressed() {
        DrawerLayout drawer = (DrawerLayout) findViewById(R.id.drawer_layout);
        if (drawer.isDrawerOpen(GravityCompat.START)) {
            drawer.closeDrawer(GravityCompat.START);
        } else {
            super.onBackPressed();
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

        //noinspection SimplifiableIfStatement
        if (id == R.id.action_settings) {
            return true;
        }

        return super.onOptionsItemSelected(item);
    }

    @SuppressWarnings("StatementWithEmptyBody")
    @Override
    public boolean onNavigationItemSelected(MenuItem item) {
        // Handle navigation view item clicks here.
        int id = item.getItemId();

        if (id == R.id.nav_adddoc) {
            Intent myIntent = new Intent(admin_Menu.this,admin_add.class);
            myIntent.putExtra("type","doctor");
            startActivity(myIntent);
        }
        if (id == R.id.nav_addpat) {
            Intent myIntent = new Intent(admin_Menu.this,admin_add.class);
            myIntent.putExtra("type","patient");
            startActivity(myIntent);
        }
        if (id == R.id.nav_addshop) {
            Intent myIntent = new Intent(admin_Menu.this,admin_add.class);
            myIntent.putExtra("type","shop");
            startActivity(myIntent);
        }

        if (id == R.id.nav_viewuser) {
            Intent myIntent = new Intent(admin_Menu.this,admin_view_users.class);
            startActivity(myIntent);
        }

        if (id == R.id.nav_setup) {
            Intent myIntent = new Intent(admin_Menu.this,admin_settings.class);
            startActivity(myIntent);
        }

        if (id == R.id.nav_reset) {
            new resetall().execute();
        }


        if (id == R.id.nav_manual) {
            new manual().execute();
        }

        else if (id == R.id.nav_logout) {
            Intent myIntent = new Intent(admin_Menu.this,LoginActivity.class);
            startActivity(myIntent);
        }

        DrawerLayout drawer = (DrawerLayout) findViewById(R.id.drawer_layout);
        drawer.closeDrawer(GravityCompat.START);
        return true;
    }


    private class manual extends AsyncTask<Void, Void, Void> {
        ProgressDialog pDialog;
        @Override
        protected void onPreExecute() {
            super.onPreExecute();
            pDialog = new ProgressDialog(admin_Menu.this);
            pDialog.setMessage("Please wait...");
            pDialog.setCancelable(false);
            pDialog.show();

        }

        @Override
        protected Void doInBackground(Void... arg0) {
            ServiceHandler sh = new ServiceHandler();
            response = sh.makeServiceCall(Config.manual, ServiceHandler.GET);
            Log.d("PRINT", url + " " + response);
            return null;
        }

        @Override
        protected void onPostExecute(Void result) {
            super.onPostExecute(result);
            if (pDialog.isShowing())
                pDialog.dismiss();
            showalert(response);
        }

    }
    private class resetall extends AsyncTask<Void, Void, Void> {
        ProgressDialog pDialog;
        @Override
        protected void onPreExecute() {
            super.onPreExecute();
            pDialog = new ProgressDialog(admin_Menu.this);
            pDialog.setMessage("Please wait...");
            pDialog.setCancelable(false);
            pDialog.show();

        }

        @Override
        protected Void doInBackground(Void... arg0) {
            ServiceHandler sh = new ServiceHandler();
            response = sh.makeServiceCall(url, ServiceHandler.GET);


            Log.d("PRINT", url+" "+ response);


            return null;
        }

        @Override
        protected void onPostExecute(Void result) {
            super.onPostExecute(result);
            if (pDialog.isShowing())
                pDialog.dismiss();

            Toast.makeText(getApplicationContext(), response, Toast.LENGTH_LONG).show();

        }

    }

    public void showalert(String msg)    {
        AlertDialog.Builder builder  = new AlertDialog.Builder(admin_Menu.this);
        builder.setMessage(msg);
        builder.setTitle("Response !");
        builder.setCancelable(false);
        builder.setPositiveButton("Ok",new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog,
                                int which)
            {
                dialog.cancel();
            }
        });

        builder.setNegativeButton("Close", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                dialog.cancel();
            }
        });


        AlertDialog alertDialog = builder.create();
        alertDialog.show();
    }

}


#admin_view_users.java

package example.com.helpdesk;

import android.app.ListActivity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.AsyncTask;
import android.os.Bundle;
import android.util.Log;
import android.widget.ListAdapter;
import android.widget.ListView;
import android.widget.SimpleAdapter;

import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import java.util.ArrayList;
import java.util.HashMap;

public class admin_view_users extends ListActivity {

    private static String iurl_get_keys = Config.view_userlist;
    private static String url_get_keys = Config.view_userlist;
    private static final String TAG_CONTACTS = "results";
    private static final String TAG_0   = "uid";
    private static final String TAG_1   = "uname";
    private static final String TAG_2   = "role";



    JSONArray contacts = null;

    ListView oplv1;
    ArrayList<HashMap<String, String>> contactList;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_admin_view_users);
        oplv1 = getListView();
        contactList = new ArrayList<HashMap<String, String>>();

        url_get_keys = iurl_get_keys+"?view=old";
        new get_details().execute();

    }
    private class get_details extends AsyncTask<Void, Void, Void> {
        ProgressDialog pDialog;
        @Override
        protected void onPreExecute() {
            super.onPreExecute();
            pDialog = new ProgressDialog(admin_view_users.this);
            pDialog.setMessage("Please Wait..");
            pDialog.setCancelable(false);
            pDialog.show();

        }

        @Override
        protected Void doInBackground(Void... arg0) {
            ServiceHandler sh = new ServiceHandler();
            String jsonStr = sh.makeServiceCall(url_get_keys, ServiceHandler.GET);
            Log.d("PRINT", url_get_keys + " response: " + jsonStr);
            if (jsonStr != null) {
                try {
                    JSONObject jsonObj = new JSONObject(jsonStr);
                    contacts = jsonObj.getJSONArray(TAG_CONTACTS);

                    for (int i = 0; i < contacts.length(); i++) {
                        JSONObject c = contacts.getJSONObject(i);
                        String opt0 = c.getString(TAG_0);
                        String opt1 = c.getString(TAG_1);
                        String opt2 = c.getString(TAG_2);


                            HashMap<String, String> contact = new HashMap<String, String>();
                            contact.put(TAG_0, opt0);
                            contact.put(TAG_1, opt1);
                            contact.put(TAG_2, opt2);

                            contactList.add(contact);

                    }


                } catch (JSONException e) {
                    e.printStackTrace();
                }
            } else {
                Log.d("PRINT", "Couldn't get any data from the url");
            }
            return null;
        }

        @Override
        protected void onPostExecute(Void result) {
            super.onPostExecute(result);
            if (pDialog.isShowing())
                pDialog.dismiss();
            ListAdapter adapter = new SimpleAdapter(admin_view_users.this, contactList,
                    R.layout.list_item_users, new String[] { TAG_0,TAG_1,TAG_2}, new int[] { R.id.tv0,R.id.tv1,R.id.tv2});

            setListAdapter(adapter);

        }

    }



    @Override
    public void onBackPressed() {
        Intent intent = new Intent(admin_view_users.this,admin_Menu.class);
        startActivity(intent);
        finish();
    }
}



# LoginActivity.java

package example.com.helpdesk;

import android.app.ProgressDialog;
import android.content.Context;
import android.content.SharedPreferences;
import android.os.AsyncTask;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;

import android.content.Intent;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Spinner;
import android.widget.TextView;
import android.widget.Toast;

import java.util.ArrayList;
import java.util.List;


public class LoginActivity extends AppCompatActivity {
    private static final String TAG = "LoginActivity";
    private static final int REQUEST_SIGNUP = 0;

    EditText _emailText;
    EditText _passwordText;
    Button _loginButton;
    Spinner spinner_utype;

    String userid="null";
    String pwd="null";
    String ltype = "null";

    String response="";
    private static String iurl = Config.logincheck;
    private static String url = iurl;


    SharedPreferences prefs ;
    SharedPreferences.Editor editor;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login);

        _emailText    = (EditText) findViewById(R.id.input_email);
        _passwordText = (EditText) findViewById(R.id.input_password);
        _loginButton  = (Button) findViewById(R.id.btn_login);
        spinner_utype   = (Spinner) findViewById(R.id.input_usertype);

        List<String> utype = new ArrayList<String>();
        utype.add("Admin");
        utype.add("Doctor");
        utype.add("Patient");
        utype.add("ShopKeeper");
        ArrayAdapter<String> dataAdapter1 = new ArrayAdapter<String>(this, android.R.layout.simple_spinner_item, utype);
        dataAdapter1.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
        spinner_utype.setAdapter(dataAdapter1);
        spinner_utype.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
            @Override
            public void onItemSelected(AdapterView<?> arg0, View arg1, int arg2, long arg3) {
                ltype = spinner_utype.getSelectedItem().toString();
            }

            @Override
            public void onNothingSelected(AdapterView<?> arg0) {
                Toast.makeText(getBaseContext(), "Please Select User Type", Toast.LENGTH_LONG).show();
            }
        });


        prefs = getSharedPreferences("UserDetails", Context.MODE_PRIVATE);
        editor = prefs.edit();

        _loginButton.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View v) {
                login();
            }
        });

    }

    public void login() {
        Log.d(TAG, "Login");

        if (!validate()) {
            onLoginFailed();
            return;
        }
        else {
            userid = _emailText.getText().toString();
            pwd = _passwordText.getText().toString();
            ltype = spinner_utype.getSelectedItem().toString();

            if (userid != "null" && pwd != "null") {
                if(userid.equals("admin") && pwd.equals("admin")) {
                    Intent myIntent = new Intent(LoginActivity.this, admin_Menu.class);
                    startActivity(myIntent);
                    finish();
                }
                else {
                    url = iurl+"?id="+userid+"&pwd="+pwd+"&type="+ltype;
                    new logincheck().execute();
                }
            } else {
                onLoginFailed();
            }
        }

    }


    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        if (requestCode == REQUEST_SIGNUP) {
            if (resultCode == RESULT_OK) {

                // TODO: Implement successful signup logic here
                // By default we just finish the Activity and log them in automatically
                this.finish();
            }
        }
    }

    @Override
    public void onBackPressed() {
        moveTaskToBack(true);
    }


    public void onLoginFailed() {
        Toast.makeText(getBaseContext(), "Login failed", Toast.LENGTH_LONG).show();
        _loginButton.setEnabled(true);
    }

    public boolean validate() {
        boolean valid = true;

        String email = _emailText.getText().toString();
        String password = _passwordText.getText().toString();

//        if (email.isEmpty() || !android.util.Patterns.EMAIL_ADDRESS.matcher(email).matches()) {
        if (email.isEmpty()) {
            _emailText.setError("enter a valid user id");
            valid = false;
        } else {
            _emailText.setError(null);
        }

//        if (password.isEmpty() || password.length() < 4 || password.length() > 10) {
        if (password.isEmpty()) {
            _passwordText.setError("password should not be empty");
            valid = false;
        } else {
            _passwordText.setError(null);
        }

        return valid;
    }


    private class logincheck extends AsyncTask<Void, Void, Void> {
        ProgressDialog pDialog;
        @Override
        protected void onPreExecute() {
            super.onPreExecute();
            pDialog = new ProgressDialog(LoginActivity.this);
            pDialog.setMessage("Please wait...");
            pDialog.setCancelable(false);
            pDialog.show();

        }

        @Override
        protected Void doInBackground(Void... arg0) {
            ServiceHandler sh = new ServiceHandler();
            String jsonStr = sh.makeServiceCall(url, ServiceHandler.GET);

            Log.d("Response: ", "> " + jsonStr);
            Log.d("PRINT", url+" "+ jsonStr);
            response=jsonStr;

            return null;
        }

        @Override
        protected void onPostExecute(Void result) {
            super.onPostExecute(result);
            if (pDialog.isShowing())
            pDialog.dismiss();

            response = response.replaceAll("\\s","");
            String[] received = response.split(":");
            if(received.length>0) {
                if(received[0].equals("WRONGPASSWORD")) {
                    Toast.makeText(getApplicationContext(), "Password Mismatch", Toast.LENGTH_LONG).show();
                }
                if(received[0].equals("NO")) {
                    Toast.makeText(getApplicationContext(), "Invalid User Credentials", Toast.LENGTH_LONG).show();
                }
                if(received[0]=="YES" || received[0].equals("YES")) {
                    Toast.makeText(getApplicationContext(), "Thanks", Toast.LENGTH_LONG).show();
                    editor.putString("login", received[1]);
                    editor.putString("profileid", received[1]);
                    editor.putString("profiletype", received[2]);
                    editor.commit();
                    Log.d("PRINT", "assign login: " + received[1] + "  profile: " + received[1]);
                    Log.d("PRINT", prefs.getString("login", "").toString() + "  " + prefs.getString("profileid", "").toString());

                    if(received[2].equals("Doctor") || received[2]=="Doctor") {
                        Intent myIntent = new Intent(LoginActivity.this, MainActivity1.class);
                        startActivity(myIntent);
                        finish();
                    }
                    if(received[2].equals("Patient") || received[2]=="Patient") {
                        Intent myIntent = new Intent(LoginActivity.this, MainActivity2.class);
                        startActivity(myIntent);
                        finish();
                    }
                    if(received[2].equals("ShopKeeper") || received[2]=="ShopKeeper" || received[2]=="shop" || received[2].equals("shop")) {
                        Intent myIntent = new Intent(LoginActivity.this, MainActivity3.class);
                        startActivity(myIntent);
                        finish();
                    }

                   
                }

            }

        }

    }
}


# main_activity1.java

package example.com.helpdesk;

import android.app.ProgressDialog;
import android.content.Context;
import android.content.Intent;
import android.content.SharedPreferences;
import android.os.AsyncTask;
import android.os.Bundle;
import android.support.design.widget.NavigationView;
import android.support.v4.view.GravityCompat;
import android.support.v4.widget.DrawerLayout;
import android.support.v7.app.ActionBarDrawerToggle;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.Toolbar;
import android.util.Log;
import android.view.MenuItem;
import android.view.View;
import android.widget.AdapterView;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.mime.content.StringBody;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;

import java.io.IOException;
import java.util.List;

import libmultispinner.MultiSelectionSpinner;


public class MainActivity1 extends AppCompatActivity implements MultiSelectionSpinner.OnMultipleItemsSelectedListener {

    EditText et_pid,et_date,et_iss,et_pre,et_dur;
    String gpid,gdate,giss,gpre,gdur;
    MultiSelectionSpinner spinner_intake;
    String loggedin,loggedin_type = "";
    String final_intake = "Nill";
    Button submit;
    String response = "";

    long totalSize = 0;
       @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main1);


        SharedPreferences prefs = getSharedPreferences("UserDetails", Context.MODE_PRIVATE);
        Log.d("PRINT", "MA1 oncreate logged in: " + prefs.getString("login", "") + "    profile id: " + prefs.getString("profileid", ""));

        prefs = getSharedPreferences("UserDetails", Context.MODE_PRIVATE);
        loggedin = prefs.getString("login", ""); loggedin = loggedin.replaceAll("\\s", "");
        loggedin_type = prefs.getString("profiletype", ""); loggedin_type = loggedin_type.replaceAll("\\s", "");

           et_pid   =  (EditText) findViewById(R.id.pre_pat);
           et_date  =   (EditText) findViewById(R.id.pre_date);
           et_iss  =   (EditText) findViewById(R.id.pre_issue);
           et_pre  =   (EditText) findViewById(R.id.pre_pre);
           et_dur  =   (EditText) findViewById(R.id.pre_dur);

           spinner_intake = (MultiSelectionSpinner) findViewById(R.id.pre_intake);
           String[] array = {"Morning", "Noon", "Night"};
           spinner_intake.setItems(array);
           spinner_intake.setSelection(new int[]{1});
           spinner_intake.setListener(this);

           spinner_intake.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
               @Override
               public void onItemSelected(AdapterView<?> arg0, View arg1, int arg2, long arg3) {
                   //product(spinner1.getSelectedItem().toString());
                   final_intake = spinner_intake.getSelectedItem().toString();
                   final_intake = final_intake.replace(" ", "");
                   Log.d("PRINT", "final_intake " + final_intake);
               }

               @Override
               public void onNothingSelected(AdapterView<?> arg0) {
                   // TODO Auto-generated method stub
               }
           });

           submit     = (Button) findViewById(R.id.pre_add);
           submit.setOnClickListener(new View.OnClickListener() {

               @Override
               public void onClick(View v) {

                   gpid           = et_pid.getText().toString();
                   gdate          = et_date.getText().toString();
                   giss           = et_iss.getText().toString();
                   gpre           = et_pre.getText().toString();
                   gdur           = et_dur.getText().toString();

                   if(final_intake!="Nill" &&  gpid!="" && gdate!="" && giss!="" && gpre!="" && gdur!="" ) {
                       new UploadData().execute();
                   }
                   else {
                       Toast.makeText(getApplicationContext(), "Please Enter Proper Details ", Toast.LENGTH_LONG).show();
                   }

               }
           });


    }


    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        int id = item.getItemId();
        if (id == R.id.action_settings) {
            return true;
        }
        return super.onOptionsItemSelected(item);
    }

    @Override
    public void selectedIndices(List<Integer> indices) {

    }

    @Override
    public void selectedStrings(List<String> strings) {
        Toast.makeText(this, strings.toString(), Toast.LENGTH_LONG).show();
        Log.d("PRINT","selectedStrings "+strings.toString());
        String local = strings.toString();
        local = local.replace("[","");
        local = local.replace("]","");
        local = local.replace(" ","");
        Log.d("PRINT","selectedStrings "+local);

        String[] allocal = local.split(",");
        Log.d("PRINT","allocal "+allocal.length);
        for(int i=0;i<allocal.length;i++) {
            Log.d("PRINT","allocal "+i+allocal[i]);
            if(i>0) {
                final_intake = final_intake + "&" + allocal[i];
            }
            else {
                final_intake = allocal[i];
            }
        }
        Log.d("PRINT","final__intake1: "+final_intake);
    }



    private class UploadData extends AsyncTask<Void, Integer, String> {
        ProgressDialog pDialog;
        @Override
        protected void onPreExecute() {
            // setting progress bar to zero
            super.onPreExecute();
            pDialog = new ProgressDialog(MainActivity1.this);
            pDialog.setMessage("Please wait...");
            pDialog.setCancelable(false);
            pDialog.show();
        }

        @Override
        protected String doInBackground(Void... params) {
            return uploadFile();
        }

        @SuppressWarnings("deprecation")
        private String uploadFile() {
            String responseString = null;

            HttpClient httpclient = new DefaultHttpClient();
            HttpPost httppost = new HttpPost(Config.prescribe);

            try {
                AndroidMultiPartEntity entity = new AndroidMultiPartEntity(
                        new AndroidMultiPartEntity.ProgressListener() {

                            @Override
                            public void transferred(long num) {
                                publishProgress((int) ((num / (float) totalSize) * 100));
                            }
                        });

                entity.addPart("pre_doc",new StringBody(loggedin));
                entity.addPart("pre_pat",new StringBody(gpid));
                entity.addPart("pre_date",new StringBody(gdate));
                entity.addPart("pre_issue",new StringBody(giss));
                entity.addPart("pre_prescribed",new StringBody(gpre));
                entity.addPart("pre_duration",new StringBody(gdur));
                entity.addPart("pre_time",new StringBody(final_intake));

                totalSize = entity.getContentLength();
                httppost.setEntity(entity);

                // Making server call
                HttpResponse response = httpclient.execute(httppost);
                HttpEntity r_entity = response.getEntity();

                int statusCode = response.getStatusLine().getStatusCode();
                if (statusCode == 200) {
                    // Server response
                    responseString = EntityUtils.toString(r_entity);
                } else {
                    responseString = "Error occurred! Http Status Code: "+ statusCode;
                }

            } catch (ClientProtocolException e) {
                responseString = e.toString();
            } catch (IOException e) {
                responseString = e.toString();
            }

            return responseString;

        }

        @Override
        protected void onPostExecute(String result) {
            Log.d("PRINT", "Response from server: " + result);
            if (pDialog.isShowing())
                pDialog.dismiss();
            response = result;
            response = response.replaceAll("\\s+","");

            if (result.equals("inserted")) {
                Toast.makeText(getApplicationContext(), "Prescription Details Added Successfully", Toast.LENGTH_LONG).show();

            }
            else if(result.equals("insertfailed")) {
                Toast.makeText(getApplicationContext(), "Unable To Add Prescription Details", Toast.LENGTH_LONG).show();
            }
            else if(result.equals("inputfailed")) {
                Toast.makeText(getApplicationContext(), "Please Enter Proper Details ", Toast.LENGTH_LONG).show();
            }

            else {
                Toast.makeText(getApplicationContext(), result, Toast.LENGTH_LONG).show();
            }

            super.onPostExecute(result);
        }

    }

    @Override
    public void onBackPressed() {
        super.onBackPressed();
            Intent intent = new Intent(MainActivity1.this, LoginActivity.class);
            startActivity(intent);

    }
}


#Main_activity2.java

package example.com.helpdesk;

import android.app.ListActivity;
import android.app.ProgressDialog;
import android.content.Context;
import android.content.Intent;
import android.content.SharedPreferences;
import android.os.AsyncTask;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.ListAdapter;
import android.widget.ListView;
import android.widget.SimpleAdapter;
import android.widget.Toast;

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.mime.content.StringBody;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import java.io.IOException;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.HashMap;

public class MainActivity2 extends ListActivity {

    private static String iurl = Config.view_requests;
    private static String url = iurl;
    private static final String TAG_CONTACTS = "results";
    private static final String TAG_0   = "doctor";
    private static final String TAG_1   = "patient";
    private static final String TAG_2   = "issue";
    private static final String TAG_3   = "prescription";
    private static final String TAG_4   = "duration";
    private static final String TAG_5   = "pre_date";
    private static final String TAG_6   = "intake";
Button feedback,empty;

    JSONArray contacts = null;


    ListView oplv1;
    ArrayList<HashMap<String, String>> contactList;

    String loggedin,loggedin_type = "";

    long totalSize = 0;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_patient_view);
        oplv1 = getListView();
        contactList = new ArrayList<HashMap<String, String>>();

        SharedPreferences prefs = getSharedPreferences("UserDetails", Context.MODE_PRIVATE);
        Log.d("PRINT", "MA1 oncreate logged in: " + prefs.getString("login", "") + "    profile id: " + prefs.getString("profileid", ""));

        prefs = getSharedPreferences("UserDetails", Context.MODE_PRIVATE);
        loggedin = prefs.getString("login", ""); loggedin = loggedin.replaceAll("\\s", "");
        loggedin_type = prefs.getString("profiletype", ""); loggedin_type = loggedin_type.replaceAll("\\s", "");

        url= iurl+"view="+loggedin;
        new get_details().execute();

        feedback = (Button) findViewById(R.id.feedback);
        empty  = (Button) findViewById(R.id.empty);
        feedback.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {

                    new Sendfeedback().execute();

            }
        });

        empty.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {

                new Sendemptydata().execute();

            }
        });

    }
    private class get_details extends AsyncTask<Void, Void, Void> {
        ProgressDialog pDialog;
        @Override
        protected void onPreExecute() {
            super.onPreExecute();
            pDialog = new ProgressDialog(MainActivity2.this);
            pDialog.setMessage("Please Wait..");
            pDialog.setCancelable(false);
            pDialog.show();

        }

        @Override
        protected Void doInBackground(Void... arg0) {
            ServiceHandler sh = new ServiceHandler();
            String jsonStr = sh.makeServiceCall(url, ServiceHandler.GET);
            Log.d("PRINT", url + " response: " + jsonStr);
            if (jsonStr != null) {
                try {
                    JSONObject jsonObj = new JSONObject(jsonStr);
                    contacts = jsonObj.getJSONArray(TAG_CONTACTS);

                    for (int i = 0; i < contacts.length(); i++) {
                        JSONObject c = contacts.getJSONObject(i);
                        String opt0 = c.getString(TAG_0);
                        String opt1 = c.getString(TAG_1);
                        String opt2 = c.getString(TAG_2);
                        String opt3 = c.getString(TAG_3);
                        String opt4 = c.getString(TAG_4);
                        String opt5 = c.getString(TAG_5);
                        String opt6 = c.getString(TAG_6);

                            HashMap<String, String> contact = new HashMap<String, String>();
                            contact.put(TAG_0, opt0);
                            contact.put(TAG_1, opt1);
                            contact.put(TAG_2, opt2);
                            contact.put(TAG_3, opt3);
                            contact.put(TAG_4, opt4);
                            contact.put(TAG_5, opt5);
                            contact.put(TAG_6, opt6);
                            contactList.add(contact);

                    }


                } catch (JSONException e) {
                    e.printStackTrace();
                }
            } else {
                Log.d("PRINT", "Couldn't get any data from the url");
            }
            return null;
        }

        @Override
        protected void onPostExecute(Void result) {
            super.onPostExecute(result);
            if (pDialog.isShowing())
                pDialog.dismiss();
            ListAdapter adapter = new SimpleAdapter(MainActivity2.this, contactList,
                    R.layout.list_item_doc_requests, new String[] { TAG_0,TAG_1,TAG_2,TAG_3,TAG_4,TAG_5,TAG_6}, new int[] { R.id.tv0,R.id.tv1,R.id.tv2,R.id.tv3,R.id.tv4,R.id.tv5,R.id.tv6});

            setListAdapter(adapter);

        }

    }


    private class Sendfeedback extends AsyncTask<Void, Integer, String> {
        ProgressDialog pDialog;
        @Override
        protected void onPreExecute() {
            // setting progress bar to zero
            super.onPreExecute();
            pDialog = new ProgressDialog(MainActivity2.this);
            pDialog.setMessage("Please wait...");
            pDialog.setCancelable(false);
            pDialog.show();
        }

        @Override
        protected String doInBackground(Void... params) {
            return uploadFile();
        }

        @SuppressWarnings("deprecation")
        private String uploadFile() {
            String responseString = null;

            HttpClient httpclient = new DefaultHttpClient();
            HttpPost httppost = new HttpPost(Config.feedback);

            try {
                AndroidMultiPartEntity entity = new AndroidMultiPartEntity(
                        new AndroidMultiPartEntity.ProgressListener() {

                            @Override
                            public void transferred(long num) {
                                publishProgress((int) ((num / (float) totalSize) * 100));
                            }
                        });

                DateFormat dateFormat = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss");
                Date date = new Date();

                Log.d("PRINT","url; "+Config.feedback);
                entity.addPart("feedback_uid",new StringBody(loggedin));
                entity.addPart("feedback_msg",new StringBody("taken"));
                entity.addPart("feedback_time",new StringBody(date.toString()));

                totalSize = entity.getContentLength();
                httppost.setEntity(entity);

                // Making server call
                HttpResponse response = httpclient.execute(httppost);
                HttpEntity r_entity = response.getEntity();

                int statusCode = response.getStatusLine().getStatusCode();
                if (statusCode == 200) {
                    // Server response
                    responseString = EntityUtils.toString(r_entity);
                } else {
                    responseString = "Error occurred! Http Status Code: "+ statusCode;
                }

            } catch (ClientProtocolException e) {
                responseString = e.toString();
            } catch (IOException e) {
                responseString = e.toString();
            }

            return responseString;

        }

        @Override
        protected void onPostExecute(String result) {
            Log.d("PRINT", "Response from server: " + result);
            if (pDialog.isShowing())
                pDialog.dismiss();

                Toast.makeText(getApplicationContext(), result, Toast.LENGTH_LONG).show();
            }



    }

    private class Sendemptydata extends AsyncTask<Void, Integer, String> {
        ProgressDialog pDialog;
        @Override
        protected void onPreExecute() {
            // setting progress bar to zero
            super.onPreExecute();
            pDialog = new ProgressDialog(MainActivity2.this);
            pDialog.setMessage("Please wait...");
            pDialog.setCancelable(false);
            pDialog.show();
        }

        @Override
        protected String doInBackground(Void... params) {
            return uploadFile();
        }

        @SuppressWarnings("deprecation")
        private String uploadFile() {
            String responseString = null;

            HttpClient httpclient = new DefaultHttpClient();
            HttpPost httppost = new HttpPost(Config.empty);

            try {
                AndroidMultiPartEntity entity = new AndroidMultiPartEntity(
                        new AndroidMultiPartEntity.ProgressListener() {

                            @Override
                            public void transferred(long num) {
                                publishProgress((int) ((num / (float) totalSize) * 100));
                            }
                        });

                DateFormat dateFormat = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss");
                Date date = new Date();

                Log.d("PRINT","url; "+Config.empty);
                entity.addPart("feedback_uid",new StringBody(loggedin));
                entity.addPart("feedback_msg",new StringBody("taken"));
                entity.addPart("feedback_time",new StringBody(date.toString()));

                totalSize = entity.getContentLength();
                httppost.setEntity(entity);

                // Making server call
                HttpResponse response = httpclient.execute(httppost);
                HttpEntity r_entity = response.getEntity();

                int statusCode = response.getStatusLine().getStatusCode();
                if (statusCode == 200) {
                    // Server response
                    responseString = EntityUtils.toString(r_entity);
                } else {
                    responseString = "Error occurred! Http Status Code: "+ statusCode;
                }

            } catch (ClientProtocolException e) {
                responseString = e.toString();
            } catch (IOException e) {
                responseString = e.toString();
            }

            return responseString;

        }

        @Override
        protected void onPostExecute(String result) {
            Log.d("PRINT", "Response from server: " + result);
            if (pDialog.isShowing())
                pDialog.dismiss();

            Toast.makeText(getApplicationContext(), result, Toast.LENGTH_LONG).show();
        }



    }

    @Override
    public void onBackPressed() {
        Intent intent = new Intent(MainActivity2.this,LoginActivity.class);
        startActivity(intent);
        finish();
    }
}


#main_activity3.java

package example.com.helpdesk;

import android.app.AlertDialog;
import android.app.ListActivity;
import android.app.ProgressDialog;
import android.content.Context;
import android.content.DialogInterface;
import android.content.Intent;
import android.content.SharedPreferences;
import android.os.AsyncTask;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.ListAdapter;
import android.widget.ListView;
import android.widget.SimpleAdapter;
import android.widget.Toast;

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.mime.content.StringBody;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import java.io.IOException;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.HashMap;

public class MainActivity3 extends ListActivity {

    private static String iurl = Config.view_order;
    private static String url = iurl;
    private static final String TAG_CONTACTS = "results";
    private static final String TAG_0   = "patient";
    private static final String TAG_1   = "bdate";
    private static final String TAG_2   = "cno";


    JSONArray contacts = null;


    ListView oplv1;
    ArrayList<HashMap<String, String>> contactList;

    String loggedin,loggedin_type = "";

    long totalSize = 0;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_shop_view);
        oplv1 = getListView();
        contactList = new ArrayList<HashMap<String, String>>();

        SharedPreferences prefs = getSharedPreferences("UserDetails", Context.MODE_PRIVATE);
        Log.d("PRINT", "MA1 oncreate logged in: " + prefs.getString("login", "") + "    profile id: " + prefs.getString("profileid", ""));

        prefs = getSharedPreferences("UserDetails", Context.MODE_PRIVATE);
        loggedin = prefs.getString("login", ""); loggedin = loggedin.replaceAll("\\s", "");
        loggedin_type = prefs.getString("profiletype", ""); loggedin_type = loggedin_type.replaceAll("\\s", "");

        url= iurl+"view="+loggedin;
        new get_details().execute();

    }
    private class get_details extends AsyncTask<Void, Void, Void> {
        ProgressDialog pDialog;
        @Override
        protected void onPreExecute() {
            super.onPreExecute();
            pDialog = new ProgressDialog(MainActivity3.this);
            pDialog.setMessage("Please Wait..");
            pDialog.setCancelable(false);
            pDialog.show();

        }

        @Override
        protected Void doInBackground(Void... arg0) {
            ServiceHandler sh = new ServiceHandler();
            String jsonStr = sh.makeServiceCall(url, ServiceHandler.GET);
            Log.d("PRINT", url + " response: " + jsonStr);
            if (jsonStr != null) {
                try {
                    JSONObject jsonObj = new JSONObject(jsonStr);
                    contacts = jsonObj.getJSONArray(TAG_CONTACTS);

                    for (int i = 0; i < contacts.length(); i++) {
                        JSONObject c = contacts.getJSONObject(i);
                        String opt0 = c.getString(TAG_0);
                        String opt1 = c.getString(TAG_1);
                        String opt2 = c.getString(TAG_2);

                            HashMap<String, String> contact = new HashMap<String, String>();
                            contact.put(TAG_0, opt0);
                            contact.put(TAG_1, opt1);
                            contact.put(TAG_2, opt2);
                            contactList.add(contact);

                    }


                } catch (JSONException e) {
                    e.printStackTrace();
                }
            } else {
                Log.d("PRINT", "Couldn't get any data from the url");
            }
            return null;
        }

        @Override
        protected void onPostExecute(Void result) {
            super.onPostExecute(result);
            if (pDialog.isShowing())
                pDialog.dismiss();
            ListAdapter adapter = new SimpleAdapter(MainActivity3.this, contactList,
                    R.layout.list_item_order, new String[] { TAG_0,TAG_1,TAG_2}, new int[] { R.id.tv0,R.id.tv1,R.id.tv2});

            setListAdapter(adapter);

        }

    }



    @Override
    public void onBackPressed() {
        Intent intent = new Intent(MainActivity3.this,LoginActivity.class);
        startActivity(intent);
        finish();
    }




}




