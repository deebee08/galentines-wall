# Galentines Polaroid Wall ♡

A collaborative, real-time web application where friends can create a shared digital polaroid wall during a party. Everyone contributes photos throughout the event, and at the end, the host generates a beautiful downloadable collage as a keepsake.

## Features

- 🎨 **Real-time Collaboration**: Everyone sees new photos instantly
- 💕 **Beautiful Design**: Cute, scrapbook-style with Sniglet font
- 📱 **Mobile-First**: Works perfectly on phones and tablets
- 🎁 **Downloadable Collage**: Generate and download a beautiful final keepsake
- 🔒 **No Accounts Needed**: Anonymous, temporary rooms with 6-character codes

## Setup Instructions

### 1. Supabase Database Setup

1. **Create a Supabase account** at [supabase.com](https://supabase.com)

2. **Create a new project**

3. **Run the database schema** in the SQL Editor:

```sql
-- Create rooms table
CREATE TABLE rooms (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  room_code TEXT UNIQUE NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  host_id TEXT,
  is_active BOOLEAN DEFAULT true
);

-- Create polaroids table
CREATE TABLE polaroids (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  room_code TEXT NOT NULL,
  image_data TEXT NOT NULL,
  name TEXT NOT NULL,
  caption TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  FOREIGN KEY (room_code) REFERENCES rooms(room_code) ON DELETE CASCADE
);

-- Create indexes
CREATE INDEX idx_polaroids_room_code ON polaroids(room_code);
CREATE INDEX idx_rooms_room_code ON rooms(room_code);

-- Enable Row Level Security
ALTER TABLE rooms ENABLE ROW LEVEL SECURITY;
ALTER TABLE polaroids ENABLE ROW LEVEL SECURITY;

-- Create policies to allow public access
CREATE POLICY "Allow public read" ON rooms
  FOR SELECT USING (true);

CREATE POLICY "Allow public insert" ON rooms
  FOR INSERT WITH CHECK (true);

CREATE POLICY "Allow public read" ON polaroids
  FOR SELECT USING (true);

CREATE POLICY "Allow public insert" ON polaroids
  FOR INSERT WITH CHECK (true);
```

4. **Enable Realtime** for the polaroids table:
   - Go to Database → Replication
   - Enable replication for the `polaroids` table

5. **Get your credentials**:
   - Go to Settings → API
   - Copy your **Project URL** (SUPABASE_URL)
   - Copy your **anon/public key** (SUPABASE_KEY)

### 2. Configure Your HTML Files

1. Open `index.html` and replace:
```javascript
const SUPABASE_URL = 'YOUR_SUPABASE_URL';
const SUPABASE_KEY = 'YOUR_SUPABASE_ANON_KEY';
```

2. Open `wall.html` and replace the same values:
```javascript
const SUPABASE_URL = 'YOUR_SUPABASE_URL';
const SUPABASE_KEY = 'YOUR_SUPABASE_ANON_KEY';
```

### 3. Deploy to GitHub Pages

1. **Create a new GitHub repository**

2. **Upload files**:
   - index.html
   - wall.html
   - README.md

3. **Enable GitHub Pages**:
   - Go to Settings → Pages
   - Source: Deploy from branch
   - Branch: main (or master)
   - Folder: / (root)
   - Click Save

4. **Wait a few minutes** for deployment

5. **Access your site** at: `https://yourusername.github.io/your-repo-name/`

## How to Use

### For the Host (Party Organizer):

1. Open the website
2. Click "Create New Room"
3. Share the 6-character room code with your friends
4. Click "Enter Room" to join the wall
5. Add your own photos like everyone else
6. At the end of the party, click "Create Final Collage"
7. Click "Download Collage" to save the keepsake

### For Guests:

1. Open the website
2. Enter the room code shared by the host
3. Click "Join Room"
4. Add photos with your name and captions
5. Watch the wall fill up in real-time!
6. Download the final collage when the host creates it

## Features in Detail

### Polaroid Cards
- 250×250px square images (auto-cropped)
- Random tilt effect for authentic polaroid look
- Name and caption support
- Pop-in animation when added

### Final Collage
- 1400px width, auto height
- Pink gradient background
- 3 polaroids per row
- 8-15 decorative stickers
- Smart sticker placement (avoids photo centers and text)
- Download as high-quality PNG

### Real-time Sync
- Instant updates across all devices
- WebSocket connection for live collaboration
- No page refresh needed

## Browser Support

- ✅ Chrome (latest)
- ✅ Safari (latest)
- ✅ Firefox (latest)
- ✅ Edge (latest)
- ✅ Mobile Safari (iOS)
- ✅ Chrome Android

## Technical Stack

- **Frontend**: Vanilla HTML/CSS/JavaScript
- **Database**: Supabase (PostgreSQL)
- **Real-time**: Supabase Realtime (WebSockets)
- **Hosting**: GitHub Pages
- **Fonts**: Google Fonts (Sniglet, Quicksand)

## File Structure

```
/
├── index.html          # Landing page (create/join room)
├── wall.html           # Collaborative polaroid wall
└── README.md          # This file
```

## Troubleshooting

### Photos not appearing in real-time
- Check Supabase Realtime is enabled for polaroids table
- Check browser console for WebSocket errors
- Verify your Supabase credentials are correct

### Room code not working
- Verify the rooms table exists in Supabase
- Check RLS policies are created
- Ensure room code is exactly 6 characters

### Collage not generating
- Check browser console for errors
- Ensure at least one polaroid has been added
- Verify all images have loaded successfully

### Upload failures
- Check image size (max 5MB)
- Verify Supabase URL and API key
- Check browser console for API errors

## Support

For issues or questions:
1. Check the browser console for errors
2. Verify Supabase configuration
3. Test with a small image first
4. Try in a different browser

## Credits

Design inspired by vintage polaroid aesthetics and scrapbook styles.
Created for Galentines parties and friend gatherings ♡

---

**Made with love for creating memories together! ✨**
