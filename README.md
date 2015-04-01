DiamanteDesk Api Bundle
========================

This bundle provides extended authentication required for DiamanteDesk API and a way to expose services and its methods as RESTful API.

## Configuration

Add additional configuration in `app/etc/security.yml`

- to section providers
```yaml
        diamante_api_user:
            id:                             diamante.api.user.security.provider
```

- to section firewalls:
```yaml
        wsse_secured_diamante:
            pattern:                        ^/api/diamante/(rest|soap).*
            provider:                       diamante_api_user
            stateless:                      true
            wsse_diamante_api:              true
```

## Usage

For example you have service defined in configuration with id "entities.service.id". To expose it as RESTful API you will need 

- it should implement interface \Diamante\ApiBundle\Routing\RestServiceInterface
- methods to expose should be public and annotated with \Diamante\ApiBundle\Annotation\ApiDoc

```php
    /**
     * @ApiDoc(
     *  description="Returns all entities",
     *  uri="/entities.{_format}",
     *  method="GET",
     *  resource=true,
     *  statusCodes={
     *      200="Returned when successful",
     *      403="Returned when the user is not authorized to list entities"
     *  }
     * )
     * @return Entities[]
     */
```
- specify service in routing configuration

```
    entities_service:
        resource:     entities.service.id
        type:         diamante_rest_service
        prefix:       /api/rest/{version}/example
        requirements:
            version:  latest|v1
            _format:  xml|json
        defaults:
            version:  latest
        _format:  json
```

After this your service will be available as
 
 ```
 GET http://host/api/rest/latest/example/entities
 ```
