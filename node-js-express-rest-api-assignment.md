# Assignment: REST Service

Nota bene: this assignment was created using https://github.com/AlreadyBored/nodejs-assignments/blob/main/assignments/rest-service/assignment.md as a template. Assignment score and some other stuff were opted out for the sake of streamlining the development process.

## Description

**Create a REST API application that operates with the following entities:**

- `User` (with required attributes):
  ```typescript
  class User {
    id: string; // uuid
    login: string;
    password: string;
    // ...other entity attributes, such as email, fullname, etc.
  }
  ```

- `Artist` (with required attributes):
  ```typescript
  class Artist {
    id: string; // uuid
    name: string;
    userId: string | null; // refers to User
    // ...other entity attributes (e.g. description)
  }
  ```

- `Track` (with required attributes):
  ```typescript
  class Track {
    id: string; // uuid
    name: string;
    artistId: string | null; // refers to Artist
    albumId: string | null; // refers to Album
    duration: number; // in seconds
    // ...other entity attributes (e.g. release date)
  }
  ```

- `Album` (with required attributes):
  ```typescript
  class Album {
    id: string; // uuid v4
    name: string;
    year: number;
    artistId: string | null; // refers to Artist
    // ...other entity attributes (e.g. release date)
  }
  ```

- `Favorites` (with required attributes):
  ```typescript
  class Favorite {
    id: string: // uuid v4
    entityId: string; // can refer to either Album, Track or Artist
    // ...other entity attributes
  }
  ```

- `AccessLog` (with required attributes):
  ```typescript
  class AccessLog {
    id: string: // uuid v4
    userId: string; // refer to User
    accessDate: Date;
    action: ActionType, // Create, Read, Delete, etc.
    entity: EntityType, // Album, Track, Artist
    // ...other entity attributes
  }
  ```

**Details:**

1. For `Users`, `Artists`, `Albums`, `Tracks`, `Favorites` and `AccessLog` REST endpoints with separate router paths should be created
  * `Users` (`/user` route)
    * `GET /user?fullname=...&email=...&page=1` - get users by filter
      - Server should answer with `status code` **200** and **paginated** user records
      - Each page should consist up to 10 records by default (can be configured by an optional route parameter)
      - Records can be filtered by common user attributes (such as email or fullname)
      - Filters are optional, pagination parameters always return the first page if not provided
      - Server should answer with `status code` **404** and corresponding message if no records were found
      - (*) String filters should NOT act as a full-text search, use filters for a partial match instead
    * `GET /user/:id` - get a single user by id
      - Server should answer with `status code` **200** and and record with `id === userId` if it exists
      - Server should answer with `status code` **400** and corresponding message if `userId` is invalid (not `uuid`)
      - Server should answer with `status code` **404** and corresponding message if record with `id === userId` doesn't exist
      - Server response should not consist any sensitive data (such as login or password)
    * `POST /user/register` - create a new user
      - Server should answer with `status code` **201** and newly created record if request is valid
      - Server should answer with `status code` **400** and corresponding message if request `body` does not contain **required** fields
      - Passwords must be encrypred before storing them in a DB
    * `PUT /user/:id/password/change` - update user's password
      - Server should answer with` status code` **200** and updated record if request is valid
      - Server should answer with` status code` **400** and corresponding message if `userId` is invalid (not `uuid`)
      - Server should answer with` status code` **404** and corresponding message if record with `id === userId` doesn't exist
      - Server should answer with` status code` **403** and corresponding message if the existing password is wrong
    * `DELETE /user/:id` - remove user
      - **NOTE:** user should NOT be deleted from a DB, mark user as "inactive" instead
      - Server should answer with `status code` **204** if the record is found and removed
      - Server should answer with `status code` **400** and corresponding message if `userId` is invalid (not `uuid`)
      - Server should answer with `status code` **404** and corresponding message if record with `id === userId` doesn't exist

  * `Tracks` (`/track` route)
    * `GET /track?title=...&page=1` - get tracks by filter
      - Server should answer with `status code` **200** and **paginated** tracks records
      - Each page should consist up to 10 records by default (can be configured by an optional route parameter)
      - Records can be filtered by common track attributes
      - Filters are optional, pagination parameters always return the first page if not provided
      - Server should answer with `status code` **404** and corresponding message if no records were found
      - (*) String filters should NOT act as a full-text search, use filters for a partial match instead
    * `GET /track/by-album/:albumId` - get all tracks by album id
      - Server should answer with `status code` **200** and **paginated** tracks records
      - Each page should consist up to 10 records by default (can be configured by an optional route parameter)
      - Server should answer with `status code` **400** and corresponding message if `albumId` is invalid (not `uuid`)
      - Server should answer with `status code` **404** and corresponding message if no records were found
    * `GET /track/by-artist/:artistId` - get all tracks by artist id
      - Server should answer with `status code` **200** and **paginated** tracks records
      - Each page should consist up to 10 records by default (can be configured by an optional route parameter)
      - Server should answer with `status code` **400** and corresponding message if `artistId` is invalid (not `uuid`)
      - Server should answer with `status code` **404** and corresponding message if no records were found
    * `GET /track/:id` - get a single track by id
      - Server should answer with `status code` **200** and and record with `id === trackId` if it exists
      - Server should answer with `status code` **400** and corresponding message if `trackId` is invalid (not `uuid`)
      - Server should answer with `status code` **404** and corresponding message if record with `id === trackId` doesn't exist
    * `POST /track` - create a new track
      - Server should answer with `status code` **201** and newly created record if request is valid
      - Server should answer with `status code` **400** and corresponding message if request `body` does not contain **required** fields
      - If no artist ID is provided, search for an artist by a requested user id (or create new if not exist) and use them as an artist
    * `PUT /track/:id` - update track info
      - Server should answer with` status code` **200** and updated record if request is valid
      - Server should answer with` status code` **400** and corresponding message if `trackId` is invalid (not `uuid`)
      - Server should answer with` status code` **404** and corresponding message if record with `id === trackId` doesn't exist
    * `DELETE /track/:id` - remove track
      - Track should NOT be deleted from a DB, mark it as "hidden" instead
      - Server should answer with `status code` **204** if the record is found and removed
      - Server should answer with `status code` **400** and corresponding message if `trackId` is invalid (not `uuid`)
      - Server should answer with `status code` **404** and corresponding message if record with `id === trackId` doesn't exist

  * `Artists` (`/artist` route)
    * `GET /artist?title=...?page=1` - get artists by filter
      - Server should answer with `status code` **200** and **paginated** artists records
      - Each page should consist up to 10 records by default (can be configured by an optional route parameter)
      - Records can be filtered by common artist attributes
      - Filters are optional, pagination parameters always return the first page if not provided
      - Server should answer with `status code` **404** and corresponding message if no records were found
      - (*) String filters should NOT act as a full-text search, use filters for a partial match instead
    * `GET /artist/:id` - get single artist by id
      - Server should answer with `status code` **200** and and record with `id === artistId` if it exists
      - Server should answer with `status code` **400** and corresponding message if `artistId` is invalid (not `uuid`)
      - Server should answer with `status code` **404** and corresponding message if record with `id === artistId` doesn't exist
    * `POST /artist` - create new artist
      - Server should answer with `status code` **201** and newly created record if request is valid
      - Server should answer with `status code` **400** and corresponding message if request `body` does not contain **required** fields
    * `PUT /artist/:id` - update artist info
      - Server should answer with` status code` **200** and updated record if request is valid
      - Server should answer with` status code` **400** and corresponding message if `artist` is invalid (not `uuid`)
      - Server should answer with` status code` **404** and corresponding message if record with `id === artistId` doesn't exist
    * `DELETE /artist/:id` - remove artist
      - Artist should NOT be deleted from a DB, mark them as "hidden" instead
      - Server should answer with `status code` **204** if the record is found and removed
      - Server should answer with `status code` **400** and corresponding message if `artistId` is invalid (not `uuid`)
      - Server should answer with `status code` **404** and corresponding message if record with `id === artistId` doesn't exist

  * `Albums` (`/album` route)
    * `GET /album?title=...?page=1` - get albums by filter
      - Each page should consist up to 10 records by default (can be configured by an optional route parameter)
      - Records can be filtered by common album attributes
      - Filters are optional, pagination parameters always return the first page if not provided
      - Server should answer with `status code` **404** and corresponding message if no records were found
      - (*) String filters should NOT act as a full-text search, use filters for a partial match instead
      - Server should answer with `status code` **200** and all albums records
    * `GET /album/:id` - get single album by id
      - Server should answer with `status code` **200** and and record with `id === albumId` if it exists
      - Server should answer with `status code` **400** and corresponding message if `albumId` is invalid (not `uuid`)
      - Server should answer with `status code` **404** and corresponding message if record with `id === albumId` doesn't exist
    * `GET /album/by-artist/:artistId` - get all albums by artist id
      - Server should answer with `status code` **200** and **paginated** albums records
      - Each page should consist up to 10 records by default (can be configured by an optional route parameter)
      - Server should answer with `status code` **400** and corresponding message if `artistId` is invalid (not `uuid`)
      - Server should answer with `status code` **404** and corresponding message if no records were found
    * `POST /album` - create new album
      - Server should answer with `status code` **201** and newly created record if request is valid
      - Server should answer with `status code` **400** and corresponding message if request `body` does not contain **required** fields
    * `PUT /album/:id` - update album info
      - Server should answer with` status code` **200** and updated record if request is valid
      - Server should answer with` status code` **400** and corresponding message if `albumId` is invalid (not `uuid`)
      - Server should answer with` status code` **404** and corresponding message if record with `id === albumId` doesn't exist
    * `DELETE /album/:id` - remove album
      - Album should NOT be deleted from a DB, mark them as "hidden" instead
      - Server should answer with `status code` **204** if the record is found and removed
      - Server should answer with `status code` **400** and corresponding message if `albumId` is invalid (not `uuid`)
      - Server should answer with `status code` **404** and corresponding message if record with `id === albumId` doesn't exist

  * `Favorite`
    * `GET /user/:id/favorites` - get all favorites by user
      - Server should answer with `status code` **200** and all favorite records, split by entity types:
      ```typescript
      interface FavoritesResponse{
        artists: Artist[];
        albums: Album[];
        tracks: Track[];
      }
      ```
      - Entity IDs should NOT be exposed, show only minimal data
    * `POST /favs/track/:id` - add track to the favorites
      - Server should answer with `status code` **201** and corresponding message if track with `id === trackId` exists
      - Server should answer with `status code` **400** and corresponding message if `trackId` is invalid (not `uuid`)
      - Server should answer with `status code` **422** and corresponding message if track with `id === trackId` doesn't exist
    * `DELETE /favs/track/:id` - delete track from favorites
      - Server should answer with `status code` **204** if the track was in favorites and now it's deleted id is found and deleted
      - Server should answer with `status code` **400** and corresponding message if `trackId` is invalid (not `uuid`)
      - Server should answer with `status code` **404** and corresponding message if corresponding track is not favorite
    * `POST /favs/album/:id` - add album to the favorites
      - Server should answer with `status code` **201** and corresponding message if album with `id === albumId` exists
      - Server should answer with `status code` **400** and corresponding message if `albumId` is invalid (not `uuid`)
      - Server should answer with `status code` **422** and corresponding message if album with `id === albumId` doesn't exist
    * `DELETE /favs/album/:id` - delete album from favorites
      - Server should answer with `status code` **204** if the album was in favorites and now it's deleted id is found and deleted
      - Server should answer with `status code` **400** and corresponding message if `albumId` is invalid (not `uuid`)
      - Server should answer with `status code` **404** and corresponding message if corresponding album is not favorite
    * `POST /favs/artist/:id` - add artist to the favorites
      - Server should answer with `status code` **201** and corresponding message if artist with `id === artistId` exists
      - Server should answer with `status code` **400** and corresponding message if `artistId` is invalid (not `uuid`)
      - Server should answer with `status code` **422** and corresponding message if artist with `id === artistId` doesn't exist
    * `DELETE /favs/artist/:id` - delete artist from favorites
      - Server should answer with `status code` **204** if the artist was in favorites and now it's deleted id is found and deleted
      - Server should answer with `status code` **400** and corresponding message if `artistId` is invalid (not `uuid`)
      - Server should answer with `status code` **404** and corresponding message if corresponding artist is not favorite

  * `AccessLog`
    * `GET /access-log/by-user/:userId` - get a full log by user
      - Server should answer with `status code` **200** and **paginated** access records
      - Each page should consist up to 10 records by default (can be configured by an optional route parameter)
      - Server should answer with `status code` **400** and corresponding message if `userId` is invalid (not `uuid`)
      - Server should answer with `status code` **404** and corresponding message if no records were found

2. Use an SQL database of your choice to store data (preferrably MySQL or PostgreSQL). Add a setup guide to the README.md file. All DB parameters must be stored in `.env` file

3. Use TypeScript and Express.JS or Nest.JS framework for API

4. (*) Code should be styled using eslint/prettier or alternative.

5. All services should be covered by unit tests with at least couple of cases when applicable (use Jest as a testing framework)

6. An `application/json` format should be used for request and response body.

7. (*) Any request to routes other than `/user/register` must contain `userId` in headers. They should be logged in `AccessLog` table depending on action and entity type

8. If a user is marked as inactive - any artist that refers to this user must be marked as hidden as well

9. In list routes, inactive/hidden entities should be excluded from response

10. When you remove `Artist`, `Album` or `Track`, it's `id` should be deleted from favorites (if was there) and references to it in other entities should become `null`. For example: `Artist` is deleted => this `artistId` in corresponding `Albums`'s and `Track`'s become `null` + this artist's `id` is deleted from favorites, same logic for `Album` and `Track`.

11. Non-existing entity can't be added to `Favorite`.

12. To run the service `npm start` command should be used.

13. Service should listen on PORT `4000` by default, PORT value is stored in `.env` file.

14. Incoming requests should be validated.
