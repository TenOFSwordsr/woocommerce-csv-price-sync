# Shifteryadak WooCommerce Price-Sync Plugin Set and Feed Archive

The working collection for the `shifteryadak.ir` parts store: the newest revisions of the three
in-house price-sync plugins, two third-party WooCommerce plugins kept alongside them, and the dated
CSV feeds plus production sync logs those plugins produce. Prices flow from a scraped cookma
wholesale catalogue and four competitor shops into WooCommerce products via a per-product markup
percentage.

**Suggested repo name:** `woocommerce-csv-price-sync`
**Stack:** PHP WordPress/WooCommerce plugins (custom cron, `wp_post_meta`, cURL), CSV feeds, plain-text sync logs
**Status:** active
**Last modified:** 2026-09-03

## What it does

- `cookma-woo.php` - "WooCommerce CSV Price Sync" v4.0 plus a section the other copies lack: a bulk
  markup screen (`csv_sync_bulk_markup_page`) that rewrites `_csv_markup_percent` for many products at
  once, either by pasted product IDs or by "everything currently at X% → Y%". Prices are recomputed on
  the `every_5_hours` cron or from the product edit screen; matching keys on `شناسه (Hash ID)`.
- `others.php` - "WooCommerce CSV Price Sync – Other Sites" with the same bulk-markup addition,
  matching by `لینک محصول` across مسترکلاه، استارسیکلت، کلاه کاسکت و گازرو and setting missing
  products to out of stock.
- `check-new-product.php` - "CSV Upload & Compare for WooCommerce": upload a fresh CSV, diff against a
  reference snapshot, review changes in a tabbed admin page, apply once. Identical in all three
  sibling folders.
- `wcpe.php` - third-party "Torob / ترب product extractor" v1.3.0 (vendor plugin, token validated
  against `extractor.torob.com`), not written here.
- `profitpulse.php` - third-party "ProfitPulse" profit manager, kept as a reference implementation of
  the same markup idea.
- `project-price-manager.php` - "Project Price Manager" v1.0.0 loader only; it requires
  `includes/class-core.php`, which is not present in this folder, so it does nothing as shipped.
- Feed and log artifacts: `cookma_2026-07-30.csv` … `cookma_2026-08-20.csv` (~1.2–1.4 MB each,
  Persian headers `عنوان محصول, دسته‌بندی, ویژگی‌ها, قیمت (تومان), شناسه (Hash ID), لینک محصول`),
  `other_sites_2026-08-12.csv`, `csv-sync.log` (2.8 MB, per-product price changes and runs through
  2026-08-22), `other-sites-sync.log` (through 2026-08-12).

## Layout

```
cookma-woo.php        cookma price sync + bulk markup
others.php            other-sites price sync + bulk markup
check-new-product.php new-product detection / CSV diff and apply
wcpe.php              third-party Torob product extractor
profitpulse.php       third-party profit manager
project-price-manager.php  stub, missing its includes/ tree
cookma_2026-*.csv     dated cookma feed exports
other_sites_2026-08-12.csv
csv-sync.log, other-sites-sync.log
```

## Running it

Install a plugin as `wp-content/plugins/<slug>/<slug>.php`, then the cron schedule is created on
activation (`register_activation_hook` → `wp_schedule_event`). Dropped into the store it needs a
`cookma_YYYY-MM-DD.csv` (or `other_sites_…`) in the WordPress `ABSPATH`, and products tagged with
`_csv_hash_id` / `_csv_markup_percent`. Admin screens appear under WooCommerce.

## Notes

- The three in-house files here are the newest revision: `client-fix` and `shiftery-fix` carry older,
  byte-identical copies without the bulk-markup sections.
- The CSVs and logs are production data - a full commercial catalogue with prices, product IDs, and
  margin percentages. Do not publish this folder as-is; keep the code and gitignore `*.csv` and
  `*.log`.
- No credentials were found in the PHP or the logs, but the plugin set is meaningless without the
  private scraper that produces the CSVs (`shiftery-fix/grimoire-scrapers.php`).
- Hardcoded reference CSV URLs in `check-new-product.php` point at `cookma_2026-05-18.csv`; they are
  stale and would need bumping for the compare flow to be useful.
