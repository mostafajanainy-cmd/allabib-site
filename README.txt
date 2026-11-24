AL-LABIB WORKBOOK SITE - one code per book, one device per code
================================================================

HOW IT WORKS
- Every book copy has its own code, printed inside (like K7MQ-4WZP-X9HD).
- The buyer opens your site, types the code once, and gets in.
- That code is now locked to that phone or computer. On any other device it
  says "already used".
- If a buyer changes phone, you press "Release" next to their code in the
  admin page, and the code works again on the new device.
- Their stars are saved and follow them.

FILES IN THIS FOLDER
  index.html   the site for buyers
  admin.html   your private page: make codes, see which are used, release codes
  config.js    the ONLY file you edit (paste your Firebase settings here)

SETUP (about 20 minutes, free)
1. Go to console.firebase.google.com, click "Create a project", name it
   allabib. You can skip Google Analytics.

2. Left menu: Build > Authentication > Get started > Sign-in method.
   Turn ON two providers: "Anonymous" and "Google". Save each one.

3. Left menu: Build > Firestore Database > Create database. Pick a location
   near you, then choose "production mode".
   Open the "Rules" tab, delete everything, paste this, and change the email
   to YOUR Google email (the one you will use for admin.html). Click Publish.

   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       function isAdmin() {
         return request.auth != null
           && request.auth.token.email == 'YOUR_EMAIL@gmail.com'
           && request.auth.token.email_verified == true;
       }
       match /codes/{code} {
         allow get: if request.auth != null;
         allow list, create, delete: if isAdmin();
         allow update: if isAdmin() || (
           request.auth != null
           && resource.data.claimedBy == null
           && request.resource.data.claimedBy == request.auth.uid
           && request.resource.data.diff(resource.data)
                .affectedKeys().hasOnly(['claimedBy', 'claimedAt']));
       }
       match /users/{uid} {
         allow read, write: if request.auth != null && request.auth.uid == uid;
       }
     }
   }

4. Click the gear icon > Project settings > scroll to "Your apps" > click the
   web icon </> > give it any name > Register app. You will see a block
   called firebaseConfig. Copy the values.

5. Open config.js in Notepad. Replace  null  with your values, like this:
   window.ALLABIB_FIREBASE={apiKey:"...",authDomain:"...",projectId:"...",appId:"..."};
   Save.

6. Upload this whole folder to your host (Netlify Drop: drag the FOLDER).
   Then in Firebase: Authentication > Settings > Authorized domains > Add
   domain, and add your site's address (for example allabib.com or
   something.netlify.app, without https://).

7. Open  your-site/admin.html , click "Sign in with Google" using the email
   you put in the rules. Type how many codes you want and click Generate.
   Copy the list and give it to your printer.

TIPS
- Test first: make 2 codes, open the site on your phone and type one.
  Then open it on your computer and type the same code. You should see
  "already used on another device".
- Keep admin.html private. Only your Google account can use it.
- Codes use letters and numbers with no 0/O/1/I, so they are easy to read.
- Never share the rules with another email.

LIMITS TO KNOW
- If a buyer clears the browser's data or uses private browsing, the device
  is forgotten. Press Release for them in admin.html.
- Lesson answers are inside the page code. The code protects who can open
  the site, but a determined user could still find answers in the source.
