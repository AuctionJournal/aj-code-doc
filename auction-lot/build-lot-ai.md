- fast catalog

# Auction Lot Building

## Initial Setup For Each Auction

When a user starts creating lots:
Default values are initialized for every auction to create fast catalog.

1.  Seller
2.  Quantity

These default values are session-based and will be reset on logout.

## Fast Catalog

1. Requirements: Lot number should not exist or lot number should be QR only
2. Other Editable Fields: Seller, Quantity
3. Image Upload: User can add lot images using either media or camera capture.
4. Unused SaleOrder will be assigned from backend.
5. Lot created through fast catalog become isAuctionready : false
6. To make this lot Auction ready either it should be analysed through ai or manually edited.

## Edit catalog

Catalog editing behavior depends on data generation status.

1. Data Generated - All the fields can be editable here except lot number (immutable once created).

2. Data is not generated - Only the following fields are editable and visible in the UI (Seller, Quantity, Image).<br/>
   All other fields are hidden and non-editable in this state.

## View catalog

Can view all the lots here
