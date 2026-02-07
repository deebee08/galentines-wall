# Troubleshooting Guide

## Common Issues and Solutions

### 1. "Failed to create room" Error

**Symptoms:**
- Clicking "Create New Room" shows an error
- Room code doesn't display

**Solutions:**
1. **Check Supabase credentials:**
   - Open browser DevTools (F12)
   - Look for 401 or 403 errors in Console
   - Verify SUPABASE_URL and SUPABASE_KEY are correct in `index.html`

2. **Verify database setup:**
   - Log into Supabase dashboard
   - Go to SQL Editor
   - Run: `SELECT * FROM rooms;`
   - If table doesn't exist, run `supabase-setup.sql`

3. **Check RLS policies:**
   - Go to Authentication → Policies in Supabase
   - Verify "Allow public insert" policy exists for rooms table
   - If missing, re-run the policy creation from `supabase-setup.sql`

### 2. "Room not found" When Joining

**Symptoms:**
- Entering a valid room code shows "room not found"
- Cannot join created rooms

**Solutions:**
1. **Verify room code:**
   - Room codes are case-sensitive
   - Must be exactly 6 characters
   - Copy-paste instead of typing

2. **Check database:**
   - Log into Supabase
   - Go to Table Editor → rooms
   - Verify the room_code exists
   - Check is_active = true

3. **Test manually:**
   ```sql
   SELECT * FROM rooms WHERE room_code = 'ABC123';
   ```

### 3. Photos Not Uploading

**Symptoms:**
- "Failed to add polaroid" error
- Upload button stuck on "uploading..."

**Solutions:**
1. **Check image size:**
   - Max 5MB per image
   - Try with a smaller image
   - Compress large photos before uploading

2. **Verify database access:**
   - Open DevTools Console
   - Look for 400/500 errors
   - Check that polaroids table exists

3. **Test database insert:**
   ```sql
   INSERT INTO polaroids (room_code, image_data, name, caption)
   VALUES ('TEST01', 'data:image/png;base64,test', 'Test', 'Caption');
   ```

4. **Check browser storage:**
   - Some browsers limit base64 data size
   - Try a different browser
   - Clear browser cache

### 4. Real-time Updates Not Working

**Symptoms:**
- Other users' photos don't appear automatically
- Need to refresh page to see new polaroids

**Solutions:**
1. **Enable Supabase Realtime:**
   - Go to Database → Replication in Supabase
   - Find polaroids table
   - Enable replication
   - Wait 1-2 minutes for changes to take effect

2. **Check WebSocket connection:**
   - Open DevTools → Network tab
   - Filter by WS (WebSocket)
   - Look for connection to Supabase
   - Should show status 101 (Switching Protocols)

3. **Verify Realtime subscription:**
   - Open DevTools Console
   - Look for "WebSocket connected" message
   - If you see errors, check SUPABASE_URL format

4. **Test with two devices:**
   - Open the same room on your phone and computer
   - Add a photo from one device
   - Should appear on the other within 3 seconds

### 5. Collage Generation Issues

**Symptoms:**
- "Add some polaroids first!" when photos exist
- Collage appears blank or incomplete
- Download button doesn't appear

**Solutions:**
1. **Check polaroids array:**
   - Open DevTools Console
   - Type: `console.log(polaroids)`
   - Verify array has items

2. **Wait for images to load:**
   - Large images take time to load
   - Watch for "All images loaded" in console
   - Try with smaller images

3. **Check canvas element:**
   - Open DevTools → Elements
   - Find `<canvas id="collageCanvas">`
   - Verify it has width and height set

4. **Browser compatibility:**
   - Canvas works best in Chrome/Firefox
   - Try a different browser
   - Ensure JavaScript is enabled

### 6. Stickers Not Appearing on Collage

**Symptoms:**
- Collage generates but no stickers
- Only polaroids visible, no decorations

**Solutions:**
1. **Verify sticker data:**
   - Open DevTools Console
   - Type: `console.log(STICKERS.length)`
   - Should show 8 (number of stickers)

2. **Check sticker loading:**
   - Stickers load after polaroids
   - Wait 5-10 seconds after "Create Final Collage"
   - Watch console for errors

3. **Image loading issues:**
   - Stickers are embedded as base64
   - Check browser console for image errors
   - Verify no ad blockers interfering

### 7. Download Not Working

**Symptoms:**
- Click "Download Collage" but nothing happens
- File downloads but is blank/corrupt

**Solutions:**
1. **Generate first:**
   - Must click "Create Final Collage" before downloading
   - Wait for canvas to fully render

2. **Check browser permissions:**
   - Allow downloads in browser settings
   - Check if download was blocked (click shield icon)
   - Try right-click → Save As on canvas

3. **Canvas to Blob:**
   - Open DevTools Console
   - Look for canvas.toBlob errors
   - Try a different browser

4. **Manual download:**
   - Right-click on the visible collage
   - Select "Save image as..."
   - Save as PNG file

### 8. Mobile Issues

**Symptoms:**
- Layout broken on phone
- Can't upload photos from camera
- Buttons too small to tap

**Solutions:**
1. **Test in mobile browser:**
   - Use actual device, not desktop emulator
   - Try both Safari (iOS) and Chrome (Android)

2. **Camera access:**
   - Allow camera permissions when prompted
   - If denied, go to Settings → Safari → Camera
   - Clear site data and reload

3. **Viewport issues:**
   - Ensure `<meta name="viewport">` tag exists
   - Zoom out if content appears too large
   - Rotate to landscape if needed

4. **Touch targets:**
   - All buttons are 44px minimum
   - If too small, report as bug
   - Try landscape orientation

## Error Messages Explained

### "please select a photo! 📷"
- No image file chosen
- Click "choose a photo" button first

### "please add your name! ✨"
- Name field is empty
- Name is required for each polaroid

### "room code must be 6 characters!"
- Entered code is too short or too long
- Check the code shared by host

### "room not found! check the code."
- Room doesn't exist in database
- Verify code with host
- Room may have been inactive

### "failed to add polaroid. try again!"
- Network error or database issue
- Check internet connection
- Verify Supabase is not down
- Try uploading a different image

### "Image too large! Max 5MB please."
- Selected image exceeds 5MB
- Compress image before uploading
- Use a different photo

### "something went wrong: [error]"
- General error with details
- Copy full error message
- Check browser console
- Report if persists

## Debugging Steps

### Step 1: Check Browser Console
1. Press F12 (or Cmd+Option+I on Mac)
2. Go to Console tab
3. Look for red error messages
4. Screenshot any errors

### Step 2: Check Network Tab
1. Open DevTools (F12)
2. Go to Network tab
3. Reload page
4. Look for failed requests (red)
5. Click on failed request to see details

### Step 3: Test Database Connection
1. Open Supabase dashboard
2. Go to SQL Editor
3. Run basic queries:
```sql
SELECT COUNT(*) FROM rooms;
SELECT COUNT(*) FROM polaroids;
```

### Step 4: Verify Configuration
Check these in your HTML files:
- [ ] SUPABASE_URL starts with https://
- [ ] SUPABASE_URL ends with .supabase.co
- [ ] SUPABASE_KEY is a long string (starts with eyJ)
- [ ] No typos in table names
- [ ] File names match (index.html, wall.html)

### Step 5: Test in Incognito
1. Open browser incognito/private mode
2. Try the app
3. If it works, issue is with cache/cookies
4. Clear browser data and try again

## Still Having Issues?

If none of these solutions work:

1. **Document the issue:**
   - What were you trying to do?
   - What actually happened?
   - Any error messages?
   - Screenshots if possible

2. **Check Supabase status:**
   - Visit status.supabase.com
   - Look for ongoing incidents

3. **Try a fresh start:**
   - Create new Supabase project
   - Re-run database setup
   - Update credentials in HTML files

4. **Test with minimal data:**
   - Create new room
   - Upload single small image
   - See if basic flow works

## Quick Fixes

### Clear Everything and Start Fresh
```javascript
// Run in browser console
localStorage.clear();
location.reload();
```

### Force Reload Polaroids
```javascript
// Run in browser console on wall.html
loadPolaroids();
```

### Check Current Room Data
```javascript
// Run in browser console
console.log('Room Code:', roomCode);
console.log('Is Host:', isHost);
console.log('Polaroids:', polaroids);
```

### Manually Trigger Collage Download
```javascript
// Run in browser console after generating collage
downloadCollage();
```

---

**Remember:** Most issues are due to:
1. Incorrect Supabase credentials
2. Database tables not created
3. RLS policies not set up
4. Realtime not enabled

Double-check these first! ✨
