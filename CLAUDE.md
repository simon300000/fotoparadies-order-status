# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Architecture

This is a single-page web application that tracks DM Fotoparadise photo printing orders. The entire application is contained in `index.html` with:

- **Frontend**: Vanilla HTML/CSS/JavaScript with Tailwind CSS for styling
- **Data Storage**: Browser localStorage for client-side persistence
- **API Integration**: Fetches order status from `spot.photoprintit.com` API
- **Auto-refresh**: Smart refresh system that runs 24/7, checking every hour and refreshing orders older than 4 hours
- **Dark Mode**: Automatic system preference detection using `darkMode: 'media'`

## Key Components

### Order Tracker Module (`orderTracker`)
- Self-contained module pattern with private state
- Manages order CRUD operations and API calls
- Handles localStorage persistence with key `dmfoto_orders`
- Automatic status polling with smart scheduling

### Status System
Four main states with specific styling:
- `LOADING`: Gray badge, spinner icon
- `PROCESSING`: Blue badge, cog icon  
- `SHIPPED`: Purple badge, truck icon
- `DELIVERED`: Green badge, check-double icon

Status badges are generated dynamically in JavaScript using direct Tailwind classes rather than CSS @apply directives for better CDN compatibility.

### API Configuration
- Base URL: `https://spot.photoprintit.com/spotapi/orderInfo/forShop`
- Config ID: `1320` (DM Fotoparadise specific)
- Parameters: shop number and order number

## Development Commands

Since this is a static HTML application with no build process:

```bash
# Development - open directly in browser
open index.html

# Or serve locally (if needed)
python3 -m http.server 8000
# Then visit http://localhost:8000
```

## Architecture Details

### Styling Approach
- Uses Tailwind CSS CDN with custom color palette
- Dark mode support via `@media (prefers-color-scheme: dark)`
- Status badges use direct Tailwind classes in JavaScript for reliability
- Glass morphism effects with backdrop-blur for order cards

### Data Flow
1. User enters shop/order numbers → stored in localStorage
2. API fetch → `fetchOrderStatus()` → updates order object
3. Status mapping via `mapApiStatusToAppStatus()` 
4. UI rendering via `renderOrders()` → generates HTML with `createOrderCard()`
5. Auto-refresh logic checks age and refreshes stale orders

### Error Handling
- Network failures gracefully handled with error states
- Duplicate order prevention
- Input validation for required fields

## Development Notes

- No build process required - open `index.html` directly in browser
- Uses CDN resources (Tailwind CSS, Font Awesome)
- All functionality is client-side with no server dependencies
- Order data is stored locally and never transmitted to external servers
- Auto-refresh runs continuously (24/7) but only updates stale orders (>4 hours old)
- Dark mode automatically follows system preference without JavaScript detection