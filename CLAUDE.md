# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Architecture

This is a single-page web application that tracks DM Fotoparadise photo printing orders. The entire application is contained in `index.html` with:

- **Frontend**: Vanilla HTML/CSS/JavaScript with Tailwind CSS for styling
- **Data Storage**: Browser localStorage for client-side persistence
- **API Integration**: Fetches order status from `spot.photoprintit.com` API
- **Auto-refresh**: Smart refresh system that only runs between 7am-10pm every 4 hours

## Key Components

### Order Tracker Module (`orderTracker`)
- Self-contained module pattern with private state
- Manages order CRUD operations and API calls
- Handles localStorage persistence with key `dmfoto_orders`
- Automatic status polling with smart scheduling

### Status System
Three main states defined in `STATUS_CONFIG`:
- `LOADING`: Initial state when fetching data
- `PROCESSING`: Order is being processed
- `DELIVERED`: Order has been delivered

### API Configuration
- Base URL: `https://spot.photoprintit.com/spotapi/orderInfo/forShop`
- Config ID: `1320` (DM Fotoparadise specific)
- Parameters: shop number and order number

## Development Notes

- No build process required - open `index.html` directly in browser
- Uses CDN resources (Tailwind CSS, Font Awesome)
- All functionality is client-side with no server dependencies
- Order data is stored locally and never transmitted to external servers
- Auto-refresh respects user privacy by only running during business hours