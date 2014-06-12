#import <Foundation/Foundation.h>
#import <libxml/tree.h>

@interface NSString (USAdditions)

- (NSString *)stringByEscapingXML;
- (NSString *)stringByUnescapingXML;
- (const xmlChar *)xmlString;
- (xmlNodePtr)xmlNodeForDoc:(xmlDocPtr)doc elementName:(NSString *)elName elementNSPrefix:(NSString *)elNSPrefix;
+ (NSString *)deserializeNode:(xmlNodePtr)cur;

@end

@interface NSNumber (USAdditions)

- (xmlNodePtr)xmlNodeForDoc:(xmlDocPtr)doc elementName:(NSString *)elName elementNSPrefix:(NSString *)elNSPrefix;
+ (NSNumber *)deserializeNode:(xmlNodePtr)cur;

@end

@interface NSDecimalNumber (USAdditions) 

- (xmlNodePtr)xmlNodeForDoc:(xmlDocPtr)doc elementName:(NSString *)elName elementNSPrefix:(NSString *)elNSPrefix;
+ (NSDecimalNumber *)deserializeString:(NSString *)stringValue;
+ (NSDecimalNumber *)deserializeNode:(xmlNodePtr)cur;

@end

@interface NSDate (USAdditions)

- (xmlNodePtr)xmlNodeForDoc:(xmlDocPtr)doc elementName:(NSString *)elName elementNSPrefix:(NSString *)elNSPrefix;
+ (NSDate *)deserializeNode:(xmlNodePtr)cur;

@end

@interface NSData (USAdditions)

- (xmlNodePtr)xmlNodeForDoc:(xmlDocPtr)doc elementName:(NSString *)elName elementNSPrefix:(NSString *)elNSPrefix;
+ (NSData *)deserializeNode:(xmlNodePtr)cur;

@end

@interface NSData (MBBase64)

+ (id)dataWithBase64EncodedString:(NSString *)string; // Padding '=' characters are optional. Whitespace is ignored.
- (NSString *)base64Encoding;
@end

@interface USBoolean : NSObject <NSCoding> {
	BOOL value;
}

@property (nonatomic) BOOL boolValue;

- (id)initWithBool:(BOOL)aValue;
- (NSString *)stringValue;

- (xmlNodePtr)xmlNodeForDoc:(xmlDocPtr)doc elementName:(NSString *)elName elementNSPrefix:(NSString *)elNSPrefix;
+ (USBoolean *)deserializeNode:(xmlNodePtr)cur;

@end

@interface SOAPFault : NSObject {
	NSString *faultcode;
	NSString *faultstring;
	NSString *faultactor;
	NSString *detail;
}

@property (nonatomic) NSString *faultcode;
@property (nonatomic) NSString *faultstring;
@property (nonatomic) NSString *faultactor;
@property (nonatomic) id detail;
@property (readonly) NSString *simpleFaultString;

+ (SOAPFault *)deserializeNode:(xmlNodePtr)cur expectedExceptions:(NSDictionary *)exceptions;

@end

@protocol SOAPSignerDelegate
- (NSData *)signData:(NSData *)rawData;
- (NSData *)digestData:(NSData *)rawData;
- (NSString *)base64Encode:(NSData *)rawData;
@end

@interface SOAPSigner : NSObject {
    __weak id<SOAPSignerDelegate> delegate;
}

@property (nonatomic, weak) id<SOAPSignerDelegate> delegate;

- (id) initWithDelegate:(id<SOAPSignerDelegate>)del;
- (NSString *)signRequest:(NSString *)req;

@end

@protocol SSLCredentialsManaging
- (BOOL)canAuthenticateForAuthenticationMethod:(NSString *)authMethod;
- (BOOL)authenticateForChallenge:(NSURLAuthenticationChallenge *)challenge;
@end

@interface BasicSSLCredentialsManager : NSObject <SSLCredentialsManaging> {
    NSString *username;
    NSString *password;
}

+ (id)managerWithUsername:(NSString *)usr andPassword:(NSString *)pwd;
- (id)initWithUsername:(NSString *)usr andPassword:(NSString *)pwd;

@end

