#import <Foundation/Foundation.h>

#ifndef DEFAULT_TIME_SEPARATOR
#	define DEFAULT_TIME_SEPARATOR ':'
#endif
unichar ISO8601UnparserDefaultTimeSeparatorCharacter = DEFAULT_TIME_SEPARATOR;

static BOOL is_leap_year(NSUInteger year) {
  return \
  ((year %   4U) == 0U)
  && (((year % 100U) != 0U)
      ||  ((year % 400U) == 0U));
}

@interface NSString(ISO8601Unparsing)

//Replace all occurrences of ':' with timeSep.
- (NSString *)prepareDateFormatWithTimeSeparator:(unichar)timeSep;

@end

@implementation NSDate(ISO8601Unparsing)

#pragma mark Public methods

- (NSString *)ISO8601DateStringWithTime:(BOOL)includeTime timeSeparator:(unichar)timeSep {
  NSString *dateFormat = [(includeTime ? @"yyyy-MM-dd'T'HH:mm:ss" : @"yyyy-MM-dd") prepareDateFormatWithTimeSeparator:timeSep];
  NSDateFormatter *formatter = [[NSDateFormatter alloc] init];
  [formatter setDateFormat: dateFormat];
  [formatter setTimeZone:[NSTimeZone timeZoneWithName:@"UTC"]];

   // BUG: Set to US locale so that "AM" or "PM" doesn't automatically get appended
   // see http://stackoverflow.com/questions/143075/nsdateformatter-am-i-doing-something-wrong-or-is-this-a-bug
   // and http://developer.apple.com/library/ios/#qa/qa1480/_index.html
  formatter.locale = [[NSLocale alloc] initWithLocaleIdentifier:@"en_US_POSIX"];

  NSString *str = [formatter stringForObjectValue:self];

  if(includeTime) {
    // NSDate - all values are UTC
    str = [str stringByAppendingString: @"Z"];
  }
  return str;
}
/*Adapted from:
 *	Algorithm for Converting Gregorian Dates to ISO 8601 Week Date
 *	Rick McCarty, 1999
 *	http://personal.ecu.edu/mccartyr/ISOwdALG.txt
 */
- (NSString *)ISO8601WeekDateStringWithTime:(BOOL)includeTime timeSeparator:(unichar)timeSep {
  enum {
    monday, tuesday, wednesday, thursday, friday, saturday, sunday
  };
  enum {
    january = 1U, february, march,
    april, may, june,
    july, august, september,
    october, november, december
  };
  
  NSCalendar *gregorian = [[NSCalendar alloc]
                           initWithCalendarIdentifier:NSGregorianCalendar];
  NSDateComponents *dateComps = [gregorian components: NSYearCalendarUnit|NSMonthCalendarUnit|NSDayCalendarUnit|NSWeekdayCalendarUnit fromDate: self];
  NSInteger year = [dateComps year];
  NSUInteger week = 0U;
  NSInteger dayOfWeek = ([dateComps weekday] + 6U) % 7U;
  NSUInteger dayOfYear = [gregorian ordinalityOfUnit: NSDayCalendarUnit inUnit: NSYearCalendarUnit forDate: self];
  
  NSInteger prevYear = year - 1U;
  
  BOOL yearIsLeapYear = is_leap_year(year);
  BOOL prevYearIsLeapYear = is_leap_year(prevYear);
  
  NSUInteger YY = prevYear % 100U;
  NSUInteger C = prevYear - YY;
  NSUInteger G = YY + YY / 4U;
  NSUInteger Jan1Weekday = (((((C / 100U) % 4U) * 5U) + G) % 7U);
  
  NSUInteger weekday = ((dayOfYear + Jan1Weekday) - 1U) % 7U;

  if((dayOfYear <= (7U - Jan1Weekday)) && (Jan1Weekday > thursday)) {
    week = 52U + ((Jan1Weekday == friday) || ((Jan1Weekday == saturday) && prevYearIsLeapYear));
    --year;
  } else {
    unsigned lengthOfYear = 365U + yearIsLeapYear;
    if((lengthOfYear - dayOfYear) < (thursday - weekday)) {
      ++year;
      week = 1U;
    } else {
      NSUInteger J = dayOfYear + (sunday - weekday) + Jan1Weekday;
      week = J / 7U - (Jan1Weekday > thursday);
    }
  }
  
  NSString *timeString;
  if(includeTime) {
    NSDateFormatter *formatter = [[NSDateFormatter alloc] init];
    [formatter setDateFormat: [@"'T'HH:mm:ss'Z'" prepareDateFormatWithTimeSeparator: timeSep]];
    timeString = [formatter stringForObjectValue:self];
  } else
    timeString = @"";
  
  return [NSString stringWithFormat:@"%u-W%02u-%02u%@", (unsigned int) year, (unsigned int) week, (unsigned int) dayOfWeek + 1U, timeString];
}
- (NSString *)ISO8601OrdinalDateStringWithTime:(BOOL)includeTime timeSeparator:(unichar)timeSep {
  NSCalendar *gregorian = [[NSCalendar alloc]
                           initWithCalendarIdentifier:NSGregorianCalendar];
  NSDateComponents *dateComps = [gregorian components: NSYearCalendarUnit fromDate: self];
  NSInteger year = [dateComps year];
  NSUInteger dayOfYear = [gregorian ordinalityOfUnit: NSDayCalendarUnit inUnit: NSYearCalendarUnit forDate: self];
  NSString *timeString;

  if(includeTime) {
    NSDateFormatter *formatter = [[NSDateFormatter alloc] init];
    [formatter setDateFormat:[@"'T'HH:mm:ss'Z'" prepareDateFormatWithTimeSeparator:timeSep]];
    timeString = [formatter stringForObjectValue:self];
  } else
    timeString = @"";
  
  return [NSString stringWithFormat:@"%u-%03u%@", (unsigned int) year, (unsigned int) dayOfYear, timeString];
}

#pragma mark -

- (NSString *)ISO8601DateStringWithTime:(BOOL)includeTime {
  return [self ISO8601DateStringWithTime:includeTime timeSeparator:ISO8601UnparserDefaultTimeSeparatorCharacter];
}
- (NSString *)ISO8601WeekDateStringWithTime:(BOOL)includeTime {
  return [self ISO8601WeekDateStringWithTime:includeTime timeSeparator:ISO8601UnparserDefaultTimeSeparatorCharacter];
}
- (NSString *)ISO8601OrdinalDateStringWithTime:(BOOL)includeTime {
  return [self ISO8601OrdinalDateStringWithTime:includeTime timeSeparator:ISO8601UnparserDefaultTimeSeparatorCharacter];
}

#pragma mark -

- (NSString *)ISO8601DateStringWithTimeSeparator:(unichar)timeSep {
  return [self ISO8601DateStringWithTime:YES timeSeparator:timeSep];
}
- (NSString *)ISO8601WeekDateStringWithTimeSeparator:(unichar)timeSep {
  return [self ISO8601WeekDateStringWithTime:YES timeSeparator:timeSep];
}
- (NSString *)ISO8601OrdinalDateStringWithTimeSeparator:(unichar)timeSep {
  return [self ISO8601OrdinalDateStringWithTime:YES timeSeparator:timeSep];
}

#pragma mark -

- (NSString *)ISO8601DateString {
  return [self ISO8601DateStringWithTime:YES timeSeparator:ISO8601UnparserDefaultTimeSeparatorCharacter];
}
- (NSString *)ISO8601WeekDateString {
  return [self ISO8601WeekDateStringWithTime:YES timeSeparator:ISO8601UnparserDefaultTimeSeparatorCharacter];
}
- (NSString *)ISO8601OrdinalDateString {
  return [self ISO8601OrdinalDateStringWithTime:YES timeSeparator:ISO8601UnparserDefaultTimeSeparatorCharacter];
}

@end

@implementation NSString(ISO8601Unparsing)

//Replace all occurrences of ':' with timeSep.
- (NSString *)prepareDateFormatWithTimeSeparator:(unichar)timeSep {
  NSString *dateFormat = self;
  if(timeSep != ':') {
    NSMutableString *dateFormatMutable = [dateFormat mutableCopy];
    [dateFormatMutable replaceOccurrencesOfString:@":"
  withString:[NSString stringWithCharacters:&timeSep length:1U]
  options:NSBackwardsSearch | NSLiteralSearch
  range:(NSRange){ 0U, [dateFormat length] }];
    dateFormat = dateFormatMutable;
  }
  return dateFormat;
}

@end
