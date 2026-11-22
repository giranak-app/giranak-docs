# ADR-0001: Initial Product Decisions

Status: Accepted
Date: 2026-11-19
Scope: Use Cases review (Guest and Provider)

## Guest and Accounts

- Guest can view Feed, Directory, and Map but cannot interact. Any write action redirects to Login.
- The API rejects any write request without a valid JWT.
- Guest selects a Community manually or by location.
- One User table with Roles. Provider is a Resident with an extra role (single account).
- Registration includes an account type choice: Resident or Provider.
- Email verification is required. Email login only in the MVP, Google login later.
- Delete Account is a soft delete.
- Languages: Arabic and English (RTL and LTR).
- Roles: Resident, Provider, Admin (a few more may be added later). Roles are stored as data in the DB.

## Provider Flow

- While the Business is Pending, the user works as a Resident.
- The Provider role is granted only after Admin approval.
- Rejection requires a reason. The user gets a notification on approve or reject.
- After rejection the user stays a Resident and can reapply after a cooldown (configurable).
- A Resident can become a Provider later through the same application and approval flow.
- One Business per Provider, with multiple locations in Premium.
- Any edit to an Approved business requires re-approval.
- Pending applications are visible to Admin only.
- Deleting a Business is a soft delete, visible to Admin only. Reviews stay linked to it.
- When Premium expires, images above 10 and offers above 3 are hidden (newest first). Nothing is deleted.
- Quick Replies are defined by the Provider (max 10).
- Premium is monthly or yearly. Price is a config value, not hardcoded.

## Chat

- Any message between two people requires approval (Resident to Provider and Resident to Resident).
- Friends are out of scope for now. The model must allow adding them later.
- Images are allowed in Business-User chat only.
- After Decline, the same cooldown applies before sending a new request.
- After Block, the blocked user cannot send again.
- Users can edit and delete their own messages, like WhatsApp.
- Anti-spam: rate limit and one pending request per person.

## Feed and Moderation

- Anonymous posts and comments hide identity in the UI but keep it in the DB.
- Only Admin can reveal an anonymous identity. Every reveal is logged in the Audit Log.
- The API must never return UserId for anonymous content.
- Posts have limits (text length and images count).
- Edit Post is allowed after interactions, with an "Edited" label and saved edit history. Auto-Filter re-runs on every edit.
- A user can delete a post that has comments (soft delete).
- A user cannot vote on their own post.
- Auto-Filter is an Include inside Create Post, Add Comment, and Send Message, not a standalone Use Case.

## Reviews

- Only Residents with an accepted chat with the business can review.
- One review per user per business. Editing is allowed.

## Notifications

- Push notification for all events: chat request, request accepted or declined, new message, business approved or rejected, report resolved.

## Scope

- Advanced Analytics is out of scope. Keep only basic counts.

## Database Impact

- IsDeleted and DeletedAt on Business (EF Core Global Query Filter).
- CreatedAt or DisplayOrder on Images and Offers.
- LastRejectedAt on the business application.
- Edit history table for posts.
- Edit and delete support for messages.

## Open Items

- Post limits: max text length and max images per post.
- Cooldown duration after reject or decline.
