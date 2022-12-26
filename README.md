# In-app-Update-alert-dialog

Set up In app update alert dialog in your android app


>Step 1.Add the ependencie build.gradle file with correct version:
```
implementation 'com.google.android.play:core:1.10.3'
```

>Step 2.Paste this code above Launcher activity's onCreate Bundle:
```
private AppUpdateManager mAppUpdateManager;
private static final int RC_APP_UPDATE = 100;
 
```

>Step 3.Paste this code inside Launcher activity's onCreate Bundle:
```
        mAppUpdateManager = AppUpdateManagerFactory.create(this);
        mAppUpdateManager.getAppUpdateInfo().addOnSuccessListener(new OnSuccessListener<AppUpdateInfo>() {
            @Override
            public void onSuccess(AppUpdateInfo result) {

                if (result.updateAvailability() == UpdateAvailability.UPDATE_AVAILABLE
                        && result.isUpdateTypeAllowed(AppUpdateType.IMMEDIATE))

                {
                    try {
                        mAppUpdateManager.startUpdateFlowForResult(result, AppUpdateType.IMMEDIATE, Today.this
                                ,RC_APP_UPDATE);

                    } catch (IntentSender.SendIntentException e) {
                        e.printStackTrace();
                    }
                }

            }
        });

```
  
  >Step 4.Paste this code after Launcher activity's onCreate Bundle:
```
 private InstallStateUpdatedListener installStateUpdatedListener = new InstallStateUpdatedListener() {
        @Override
        public void onStateUpdate(InstallState state)
        {
            if (state.installStatus() == InstallStatus.DOWNLOADED)
            {
                showCompletedUpdate();
            }
        }
    };

    @Override
    protected void onStop() {
        // if (mAppUpdateManager!=null)mAppUpdateManager.unregisterListener(installStateUpdatedListener);

        super.onStop();
    }

    private void showCompletedUpdate() {
        Snackbar snackbar = Snackbar.make(findViewById(android.R.id.content), "New Update Is Available!"
                , Snackbar.LENGTH_INDEFINITE);
        snackbar.setAction("Install", new View.OnClickListener() {
            @Override
            public void onClick(View v)
            {
                mAppUpdateManager.completeUpdate();
            }
        });
        snackbar.show();

    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data)
    {
        if (requestCode == RC_APP_UPDATE && resultCode != RESULT_OK)
        {
            Toast.makeText(this, "Cancel!", Toast.LENGTH_SHORT).show();
        }

        super.onActivityResult(requestCode, resultCode, data);
    }

    @Override
    protected void onResume() {
        super.onResume();

        mAppUpdateManager.getAppUpdateInfo().addOnSuccessListener(new OnSuccessListener<AppUpdateInfo>() {
            @Override
            public void onSuccess(AppUpdateInfo result) {

                if (result.updateAvailability() == UpdateAvailability.DEVELOPER_TRIGGERED_UPDATE_IN_PROGRESS) {
                    try {
                        mAppUpdateManager.startUpdateFlowForResult(result, AppUpdateType.IMMEDIATE, Today.this
                                , RC_APP_UPDATE);

                    } catch (IntentSender.SendIntentException e) {
                        e.printStackTrace();
                    }
                }
            }
        });

    }
```
 
