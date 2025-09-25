## Design Instagram Stories
Design a system that allows users to post ephemeral content (photos/videos) that disappears after 24 hours. The system should support:

- Posting stories (photos, videos, text overlays, filters)
- Viewing stories from people you follow
- Story highlights (saving stories permanently to profile)
- Story analytics (views, interactions)
- Real-time updates when new stories are posted
- Global scale (1B+ users, millions of stories posted daily)

## clarifying questions 
basically i think i also need to think beforehand like what i need to calc or how im gonna design so i need to ask here
1) what is the average photo/video size? for calculation
2) is there like a close friends feature too that restricts story view?
3) is there a limit to how many stories user can post or save?
4) the growth rate over the years?
5) DAU? peak concurrent number of users?

## answers
**1) Average media sizes:**
- Photos: ~2-3 MB (compressed for mobile)
- Videos: ~10-15 MB (15-30 second clips, mobile optimized)
- Mix: ~70% photos, 30% videos

**2) Close friends feature:**
- Yes, assume there's a "Close Friends" feature where users can share to a subset (~10-50 people typically)
- Also consider privacy levels: Public, Friends, Close Friends, Custom lists

**3) Story limits:**
- Posting: No hard limit, but typically users post 1-5 stories per day
- Highlights: Unlimited saves to profile highlights
- Individual story limit: Let's say max 10 photos/videos per story sequence

**4) Growth rate:**
- Assume 10-15% YoY user growth
- Story feature specifically grows faster (~20-30% YoY) as it's still relatively newer

**5) Scale assumptions:**
- Total users: ~2B
- DAU: ~500M-1B  
- Peak concurrent: ~100-200M during prime hours
- Stories posted: ~500M per day globally
- Story views: ~10B per day

**Additional assumptions:**
- Average user follows ~200 people
- Peak traffic during evening hours in major regions
- 80% mobile traffic

## functional requirements
User: 
- authentication
- follower and followed list

Media: 
- post, view, save story

Analytics: 
- views (aggregate function)
- interactions

## better
**User Management:**
- User authentication & authorization
- Follow/unfollow users
- Privacy settings (public, friends, close friends, custom lists)

**Story Lifecycle:**
- Create stories (photo/video upload with filters, text, stickers)
- View stories (chronological feed from followed users) 
- Auto-delete stories after 24 hours
- Save stories to Highlights (permanent profile collection)

**Story Interactions:**
- View story (increment view count)
- React to stories (heart, laugh, etc.)
- Reply to stories (DM to story creator)
- Share stories (repost to own story)

**Content Discovery:**
- Story feed (stories from people you follow)
- Story ring indicators (show who has new stories)
- Search within story highlights

**Analytics & Insights:**
- View counts and viewer list
- Interaction metrics (replies, shares, reactions)
- Story performance insights for creators

**What you were missing:**
- **Privacy controls** (biggest gap - very important for Meta)
- **Story interactions** beyond just viewing
- **Content discovery/feed** functionality  
- **Story sharing/reposting**
- **Auto-deletion mechanism** (core to Stories concept)

**Nice additions to consider:**
- Push notifications for new stories
- Story creation tools (filters, AR effects)
- Story polls/questions/quizzes

This shows you understand the full user journey, not just basic CRUD operations.

## Non functional
btw its giga -> tera -> peta (GTP) remember chatgpt

data:
story post = 500 10^6 * 500 10^6 * (0.7 x 3x 10^6 + 0.3 * 15 *x 10^6) = 0.25 * 10^18 (10^6) = 0.25 * 10^24 byte per day of stored data that is 7.5 x 10^24 byte per month
story view = i think its just read so we dont have to calc

im thinking highly available eventual consistency 

but thats just absurd i miscalc like i thought each user posts 500 million stories but i dont have to worry about dau here.

**Scale & Performance:**
- **Stories posted:** 500M/day 
- **Story views:** ~10B/day (20:1 read/write ratio)
- **Peak concurrent users:** 200M
- **Story load latency:** <500ms globally
- **Feed refresh latency:** <1s

**Storage Calculations:**
```
Daily story data = 500M stories × average size
Average size = (0.7 × 3MB) + (0.3 × 15MB) = 6.6MB
Daily storage = 500M × 6.6MB = 3.3 PB/day
Monthly storage = ~100 PB/month
```
*But stories delete after 24h, so steady state ~3-4 PB*

**Availability & Consistency:**
- **Availability:** 99.95% uptime
- **Consistency:** Eventual consistency for views/analytics
- **Strong consistency:** For story posting/deletion
- **Partition tolerance:** System must remain available during network splits

**Additional Requirements:**
- **Security:** End-to-end encryption for private stories
- **Compliance:** GDPR deletion, content moderation
- **Mobile optimization:** Adaptive bitrate, offline viewing
- **Global reach:** <100ms latency via CDN
- **Scalability:** Handle 50% traffic spikes during events
- **Data retention:** 24h auto-deletion, audit logs for 90 days
- **Fault tolerance:** Auto-failover, graceful degradation

**Cost optimization:** Tiered storage (hot → warm → cold → delete)

The key insight: your steady-state storage is much lower due to 24h deletion!
