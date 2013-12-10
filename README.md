# Another Musicbrainz Node Client

NodeBrainz is a thin wrapper that gives you full access to the [MusicBrainz](http://musicbrainz.org/) API ([Version 2](http://musicbrainz.org/doc/Development/XML_Web_Service/Version_2/)). This includes [search](http://musicbrainz.org/doc/Development/XML_Web_Service/Version_2/Search), [lookup](http://musicbrainz.org/doc/Development/XML_Web_Service/Version_2/#Lookups) and [browse](http://musicbrainz.org/doc/Development/XML_Web_Service/Version_2/#Browse).



### Example Usage

MusicBrainz asks that you [identifying your application](http://musicbrainz.org/doc/Development/XML_Web_Service/Version_2#Identifying_your_application_to_the_MusicBrainz_Web_Service) so be sure to set the `userAgent`. You may consider following the conventions of [RFC 1945](http://tools.ietf.org/html/rfc1945#section-3.7)

    var NB = require('nodebrainz');

    // Initialize NodeBrainz
    var nb = new NB({userAgent:'my-awesome-app/0.0.1 (+http://my-awesome-app.com)'});

Setting a custom `host`, `path` and `defaultLimit` (if not set, the `defaultLimit` is 25);

    var nb = new NB({host:'localhost', path:'/path/to/data/', defaultLimit:50});

## MusicBrainz Entities

There are eight entities: `artist`, `label`, `recording`, `release`, `release-group`, `work`, `area`, `url`

## Lookup

Lookups can be preformed on any of the eight entities.

Lookup an `artist` and include their `releases`, `release-groups` and `aliases`

    nb.artist('e0140a67-e4d1-4f13-8a01-364355bee46e', {inc:'releases+release-groups+aliases'} , function(err, response){
        console.log(response);
    });

Lookup a `release-group` with no filtering or subqueries

    nb.releaseGroup('df46f245-7f62-4982-9d2c-e83d7be91cbf', function(err, response){
        console.log(response);
    });

There are different subqueries you can include depending on the entities.

- **Arists** - `recordings`, `releases`, `release-groups`, `works`
- **Label** - `releases`
- **Recording** - `artists`, `releases`
- **Release** - `artists`, `labels`, `recordings`, `release-groups`
- **Release-group** - `artists`, `releases`

Check out the some of the additional [subqueries](http://musicbrainz.org/doc/Development/XML_Web_Service/Version_2#inc.3D_arguments_which_affect_subqueries). Note that the number of linked entities returned is always limited to 25, if you need the remaining results, you will have to perform a `browse` or `search`.

## Browse

Browse requests are a direct lookup of all the entities directly linked to another entity. For example, if you wanted to look up all the `release-groups` for a particularly talented artist:

    nb.browse('release-group', {artist:'e0140a67-e4d1-4f13-8a01-364355bee46e'}, function(err, response){
      console.log(response);
    });

Browsed entities are always ordered alphabetically by gid. If you need to sort the entities, you will have to fetch all entities and sort them yourself. For pagination, set a `limit` and `offset`.

    nb.browse('release-group', {artist:'e0140a67-e4d1-4f13-8a01-364355bee46e', type:'album', limit:2, offset:1}, function(err, response){
      console.log(response);
    });

Note that `browse` requests are not `searches`, in order to browse all the `releases-groups` for an `artist`, you need to provide the `MBID` for the `artist`.

## Search

Provides a way to search for entities. Behind the scenes, results are provided by a search server using Lucene technology.

    nb.search('artist', {artist:'tool', country:'US'}, function(err, response){
        console.log(response);
    });

### Search Fields

There are different search fields depending on the entity.

- [Artist](http://musicbrainz.org/doc/Development/XML_Web_Service/Version_2/Search#Artist)
- [Label](http://musicbrainz.org/doc/Development/XML_Web_Service/Version_2/Search#Label)
- [Recording](http://musicbrainz.org/doc/Development/XML_Web_Service/Version_2/Search#Recording)
- [Release](http://musicbrainz.org/doc/Development/XML_Web_Service/Version_2/Search#Release)
- [Release-group](http://musicbrainz.org/doc/Development/XML_Web_Service/Version_2/Search#Release_Group)
- [Work](http://musicbrainz.org/doc/Development/XML_Web_Service/Version_2/Search#Work)
- [Area](http://musicbrainz.org/doc/Development/XML_Web_Service/Version_2/Search#Area)

Search for all `releases` for the `artists` named *pink floyd*. Limited to 20 and offset by 5

    nb.search('release', {artist:'pink floyd', limit:20, offset:5}, function(err, response){
      console.log(response);
    });

Search for all the studio albums for a specific artist (identified by their MBID)

    nb.search('release-group', {arid:'e0140a67-e4d1-4f13-8a01-364355bee46e', type:"album"}, function(err, response){
      console.log(response);
    });



## To Do

- Unit tests
