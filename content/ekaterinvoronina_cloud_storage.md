## Общее
- bean injection хромает по всему проекту. Почитай про best practice, это твоя точка роста (Внедрение через @Autowired или не final поля - bad practice. Нужно через @RequiredArgsConstructor)

## docker-compose.yaml

### ⚠️ Улучшения:
- Я бы добавил healthcheck для каждого сервиса и использовал бы depends_on: condition: service_healthy
- Чтобы добавить healthcheck для основного приложения можно воспользоваться Spring Boot Actuator 

## Dockerfile

### ⚠️ Улучшения:
- JDK содержит инструменты, которые не нужны в production
- Я бы использовал мультиэтапную сборку, например FROM eclipse-temurin:21-jre-alpine (только runtime)

## build.gradle

### ⚠️ Улучшения:
- можно хранить версии зависимостей в переменных и назначать их в файле gradle.properties (централизованное хранение поможет удобно менять версии)

## Файлы конфигураций (application.properties и тд)

### ⚠️ Улучшения:
- Некоторые проперти не обнаружены (мне IDEA говорит "Cannot resolve configuration property 'jpa.properties.hibernate.dialect')
- Как правило чаще встречал на проектах yaml формат. Он помогает не дублировать префиксы у пропертей

## changeset-001

### ⚠️ Улучшения:
- Я бы использовал bigint у юзеров (скорее всего их будет много в приложении)
- Скорее всего пароль вряд ли будет 255 символов. Я бы снизил количество символов в миграции до валидной длины пароля

## ServletInitializer

### ⚠️ Улучшения:
- Не совсем понял необходимость этого класса. В Spring Boot встроен Tomcat сервер, наверное ServletInitializer тут избыточен. Проверь, работает ли без него

## AppConfig

### ⚠️ Улучшения:
- Наверное стоит дать более осознанное именование класса
- directoryService должен быть final
- Лучше @RequiredArgsConstructor (он и не даст коду с не final бинами скомпилироваться. Это хорошая практика)

## SecurityConfig

### ⚠️ Улучшения:

## AuthController

### ⚠️ Улучшения:
- В методе signOut я бы вернул ResponseEntity<Void>
- log.info("Received sign out request") - выглядит неинформативным - будет только засорять логи при большом количестве юзеров, но при этом ты не сможешь по этому логу определить какой это юзер

## DirectoryController

### ⚠️ Улучшения:
- Я бы написал @RequestMapping("/api/directory"), т.к оба метода реализуют это в маппинге

## ResourceController

### ⚠️ Улучшения:
- Я бы написал @RequestMapping("/api/resource"), т.к оба метода реализуют это в маппинге
- log.info("Received request to upload files [{}] by path [{}]", String.join(" | ", object.stream().map(MultipartFile::getOriginalFilename).toList()), path) - выглядит сложно. Преобразование можно вынести в приватный метод

## ResourceDownloadController

### ⚠️ Улучшения:
- Нужен ли для этого отдельный контроллер? Я бы перенес в ResourceController
- downloadResource не должен быть приватным

## ResourceMoveController

### ⚠️ Улучшения:
- Нужен ли для этого отдельный контроллер? Я бы перенес в ResourceController

## ResourceSearchController

### ⚠️ Улучшения:
- Нужен ли для этого отдельный контроллер? Я бы перенес в ResourceController
- searchResource не должен быть приватным
- @ResponseStatus(HttpStatus.OK) не нужна. Spring Boot по дефолту прокидывает 200 при успехе

## dto package

### ⚠️ Улучшения:
- Я бы везде использовал java record. Очень удобная фича

## dto package

### ⚠️ Улучшения:
- Я бы везде использовал java record. Очень удобная фича


## GlobalExceptionHandler

### ⚠️ Улучшения:
- В последнем setMessage обобщенный тип не используется. Можно удалить
- В ErrorResponse круто положить название эндпоинта, где произошла ошибка, а также timestamp

## ResourceServiceFactory

### ⚠️ Улучшения:
- Отличный подход с фабрикой)
- И снова bean injection хромает

## User

### ⚠️ Улучшения:
- Я бы убрал хардкод роли и завел бы enum под ROLE_USER и будущие
- Подумать о том, нужно ли тут переопределение equals & hashcode

## UserRepository

### ⚠️ Улучшения:
- @Repository не будет лишней

## ZipCreator

### ⚠️ Улучшения:
- Закоментированный код сбивает с толку
- FileEntry может использовать @Getter и @RequiredArgsConstructor

## MinioArgsFactory

### ⚠️ Улучшения:
- getObjectArgs не используется

## StorageService

### ⚠️ Улучшения:
- Я бы не стал ловить Exception. Это слишком обобщенно, лучше ловить конкретные исключения
- Возвращать из метода null - не лучшая практика. Можно потом насобирать NullPointerException. Лучше вернуть Optional - это укажет на возможное отсутствие значения более явно
- getObjectsInfo не используется

## SearchService

### ⚠️ Улучшения:
- Много закоментированного кода и неиспользуемых переменных. С этим точно надо что-то сделать

## AuthorizationService

### ⚠️ Улучшения:
- Поля должны быть final
- userRepository.findByLogin(authorizeUserRequest.getUsername()) и далее можно заменить на функциональный стиль, который сократит количество кода

## UserDetailsServiceImpl

### ⚠️ Улучшения:
- Внедрение через @Autowired - bad practice. Нужно через @RequiredArgsConstructor

## UserService

### ⚠️ Улучшения:
- Поля должны быть final
- в registerUser прослеживается бизнес-логика в обработке исключений. savedUserId будет null только в случае если что-то пойдет не так во время создания пользователя? Наверное в других случаях savedUserId не должен быть null. Если я ошибаюсь и ты уверена в своей логике, то должен быть тест на этот кейс
