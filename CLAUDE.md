# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Architecture

This is a single-page web application that tracks DM Fotoparadies photo printing orders. The entire application is contained in `index.html` with:

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
Six main states with specific styling:
- `LOADING`: Gray badge, spinner icon
- `PROCESSING`: Blue badge, cog icon
- `PRODUCED`: Purple badge, check icon
- `SHIPPED`: Indigo badge, truck icon
- `DELIVERED`: Green badge, check-double icon
- `COLLECTED`: Gray badge (dimmed), check-circle icon

Status badges are generated dynamically in JavaScript using direct Tailwind classes rather than CSS @apply directives for better CDN compatibility.

### API Configuration
Two endpoints based on order type:

**Shop-based endpoint** (when shop number is provided):
- URL: `https://spot.photoprintit.com/spotapi/orderInfo/forShop`
- Config ID: `1320` (DM Fotoparadies specific)
- Parameters: `config`, `shop`, `order`

**Full Order ID endpoint** (when shop number is empty and order is 12 digits):
- URL: `https://spot.photoprintit.com/spotapi/orderInfo/order`
- Config ID: `1320`
- Parameters: `config`, `fullOrderId`
- Accepts formats: `123321123321` or `123456-123456`

Both endpoints return the same JSON structure.

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

### Order Input Modes
The application supports two ways to track orders:

1. **Shop + Order Number**: Traditional mode requiring both fields
2. **Full Order ID**: 12-digit direct order lookup (shop number optional)

Dynamic form validation automatically toggles shop number requirement:
- `handleOrderNumberInput()` watches the order number field on every keystroke
- If order number is valid 12-digit format → shop becomes optional
- Otherwise → shop remains required

### Data Flow
1. User enters shop/order numbers → stored in localStorage
2. API endpoint selection based on input type (shop-based vs full order ID)
3. API fetch → `fetchOrderStatus()` → updates order object
4. Status mapping via `mapApiStatusToAppStatus()`
5. UI rendering via `renderOrders()` → generates HTML with `createOrderCard()`
6. Auto-refresh logic checks age and refreshes stale orders (skips delivered/collected)

### URL Syncing
Optional feature for sharing orders between devices:
- Enabled via toggle in UI (`urlSyncEnabled` localStorage key)
- Encodes latest 10 non-collected orders in URL hash (base64-encoded JSON)
- Format: `#orders={base64-encoded array of {s: shopNumber, o: orderNumber}}`
- On page load with sync enabled, imports orders from URL if not already tracked

### Error Handling
- Network failures gracefully handled with error states
- Duplicate order prevention
- Dynamic input validation (12-digit format checking via regex)

## Development Notes

- No build process required - open `index.html` directly in browser
- Uses CDN resources (Tailwind CSS, Font Awesome)
- All functionality is client-side with no server dependencies
- Order data is stored locally and never transmitted to external servers (except API status checks)
- Auto-refresh runs continuously (24/7) but only updates stale orders (>4 hours old, excludes delivered/collected)
- Dark mode automatically follows system preference without JavaScript detection

## Important Implementation Details

### localStorage Keys
- `dmfoto_orders`: Main order array (JSON)
- `lastShopNumber`: Saved shop number for convenience
- `urlSyncEnabled`: Boolean for URL sync feature
- `collectedOrdersCollapsed`: Boolean for UI state

### Order Object Structure
```javascript
{
  shopNumber: string,        // Can be empty for full order ID mode
  orderNumber: string,
  createdAt: timestamp,
  lastUpdated: timestamp,
  data: object | null,       // API response data
  status: string,            // LOADING | PROCESSING | PRODUCED | SHIPPED | DELIVERED
  isLoading: boolean,
  collected: boolean,
  collectionDate: timestamp | null
}
```

### Key Helper Functions
- `isFullOrderId(orderNumber)`: Validates 12-digit format (with/without dash)
- `fetchOrderStatus(shopNumber, orderNumber)`: Automatically selects correct API endpoint
- `handleOrderNumberInput()`: Dynamic form validation on keystroke