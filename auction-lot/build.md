[Auction Journal](../index.md)

# Auction Lot Building

## Build Through

- Create Lot
- Import Lot
- Fast Catalog
- QR only lot

## Requirements

## Intial create

## Edit Lot

## Import through csv

## Fast catalog

- Auction lot is created by assistant
- created with minimum details (lot number, images,...)
- these lots will not be auction ready
- these lots are ment be analysed by lot-ai. after that it will become auction ready

## Edit Lot Images

- To edit images or poistion of images of lots in an auction

- send theses

  - auctionDetailId
  - massImagesDetails - Its an array of lot to update images

- Each lot object

  - fields

    - img
    - oldImg - if edited
    - isExisting
    - isToDelete
    - isEdited

  - check if images are already in lot
  - if no existing images new array of images will be directly added according to pos
  - if images then following flow

- Updating lot with existing images

  - isToDelete - directly delete image and skip to next image
  - isEdited - remove old image and proceed to add new image in defined position

- Adding Image
  - isOverWriteExisting - directly overwrite existing image of that pos
  -

### [lot ai analysis](./build-lot-ai.md)
